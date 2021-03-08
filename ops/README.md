# Deploy Instructions

## AWS – Prerequisites

Set up your own account

Connect to aws-vault
https://github.com/99designs/aws-vault

Configure a region by adding
`profile=us-east-1` under the newly added profile in
`~/.aws/config`, otherwise you will have to keep specifying a region at each command.
Preface all AWS commands with `aws-vault exec ${MY_ACCOUNT_NAME}`

Note that a default VPC is created when you create a free account, you can check like this:

`aws ec2 describe-vpcs`

## Build the APP

Build a new image of the docker container
`docker build -t outyet-image .`

`docker run --publish 8080:8080 --detach --name test-outyet-image outyet-image`

struggling?
`docker inspect $MY_CONTAINER_NAME`
## Host Docker image on ECR

1. Create a new ECR repo, named `outyet-image`, via AWS console

Note: All URLS will need to be replaced with the values from your account.

2. Get creds for repo so you can push image

`aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 424795685451.dkr.ecr.us-east-1.amazonaws.com`

You should see a `Login succeeded` message to indicate you will be able to push your image.

3. Tag the image (built in the last step)
`docker tag outyet-image:latest 424795685451.dkr.ecr.us-east-1.amazonaws.com/outyet-image:latest`

4. Push the image to ECR
`docker push 424795685451.dkr.ecr.us-east-1.amazonaws.com/outyet-image:latest`
## Creating a new ECS Cluster
### Preparing a security group
Before we create a new `Cluster` in ECS, we need to make a security group that it will be able to use.

1. Create a new `Security group` named `outyet-sg`

Leave the rest of the sections empty.
### Creating a new cluster

1. Navigate to the ECS section of the AWS console and create a new cluster.
2. Choose `EC2 Linux + Networking` for the cluster template
3. Name the cluster `outyet-cluster`

Leave most things default but..

- Click 2 instances and t2.nano
- Use the default VPC, and select all subnets
- Add an ssh keypair

3. Select the previously created security group (`outyet-sg`)
## Run your docker container on EC2

Create a task definition, a blueprint to _start_ the container. Task definitions specify the container information for your application, such as what CPU and memory resources they will use, how they are linked together, and which host ports they will use.

Note: you will need to update the `image` definition in the `task-definition.json` file to match the location of your ECR repository.

`aws ecs register-task-definition --cli-input-json file://task-definition.json`
#### Gotchas

- Command and container port should match what you specified in your Dockerfile (with the `EXPOSE` definition).
## Load balancer and a target group

Load balancing algorithm; Round robin unless otherwise specified. Uses healthchecks on the instances to decide if they are "healthy" and send traffic their way. They will be "unhealthy" until we have something running on them (as they will not be responding to requests with `200` responses).

Create an ALB via console;
- Select application load balancer, name it, leave everything else the same, ensuring you have your default VPC setup.
- Select all available `Availablility zones`
- Skip the warning about SSL, this is just a test app
- Create a new Security group named `outyet-elb-sg`; open up port 80 and source 0.0.0.0/0 so anything from the outside world can access the ELB on port 80.
- Create a new target group named outyet-elb-target-group with port 80
- Register instances to your target group (registers instances to the load balancer). These instances were created for us automatically during the `Cluster` setup.

Right now, our instances (using security group `outyet-sg`) will not accept any connections from the load balancer (using security group `outyet-elb-sg`) they are part of, which means that traffic will not be passed on successfully to our instances/application containers. To rectify this, we can use the following command:

`aws ec2 authorize-security-group-ingress --group-name outyet-sg --protocol tcp --port 1-65535 --source-group outyet-elb-sg`

The above command means `For any resource that has the outyet-sg security group applied, allow access on all ports to any resources that have the outyet-elb-sg security group applied`, which effectively opens up communication between our load balancer and instances!

## Create a service that runs our task
A service defines how we want to run our task definition, including how many containers we wish to run, and how we want to register these containers with our load balancer/target group.

Jump into another cloudformation template to define this service. Use the ARN (resource name) of your new load balancer's target group in this template for the target group. Again, make sure you watch the container name and exposed port.

`aws ecs create-service --cli-input-json file://ecs-service.json`

Great! Is it working? Curl your elb. SSH into a running instance

Note to self; you had to create the ecsServiceRole yourself

https://acloud.guru/forums/aws-ecs-scaling-docker/discussion/-KoftNn-sf4KhPe6t0WZ/error%20with%20the%20service%20role
https://docs.aws.amazon.com/AmazonECS/latest/developerguide/check-service-role.html



##  Exercises
* Drain an instance and see what happens – why?
* Stop task and see what happens – why?
* How many spa node servers are running? Can you see in AWS console, in a CF template
* Curl your ELB at its public endpoint to check on your app
* CLI vs Console? Is it a one off setup, or will we need to re-do it easily, repeatedly and regularly?
* Task definition vs CloudFormation template?
* Which things need to change at every deploy?
* Which things are we changing when we rotate the stack for an AMI update?
* Teardown! Remove all traces - try to remember what we created. What order does it need to be removed in? (Important, aws may charge $$)
