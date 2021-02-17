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

1. Create ECR repo via AWS console

Get creds for repo so you can push image

`aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 424795685451.dkr.ecr.us-east-1.amazonaws.com`

Now tag the image (built in the last step)
`docker tag outyet-image:latest 424795685451.dkr.ecr.us-east-1.amazonaws.com/outyet-image:latest`

Then push to ECR
`docker push 424795685451.dkr.ecr.us-east-1.amazonaws.com/outyet-image:latest`

## ECS

Create a new Cluster fromt the AWS console

Leave most things default but..

- Click 2 instances and t2.nano
- Use the default VPC, and select all subnets
- Add an ssh keypair

AWS will make a cluster for you with two instances, and an autoscaling group based on the instance types you selected



## Run your docker container on EC2

Create a task definition, a blueprint to _start_ the container. Task definitions specify the container information for your application, such as how many containers are part of your task, what resources they will use, how they are linked together, and which host ports they will use

#### Gotchas

- Command and container port should match what you specified in your Dockerfile

Then register the task definition

`aws ecs register-task-definition --cli-input-json file://task-definition.json`

## Load balancer and a target group

Load balancing algorithm; Round robin unless otherwise specified. Uses healthchecks on the instances to decide if they are "healthy" and send traffic their way. They will be "unhealthy"  until we have something running on them.  

Create an ELB via console; 
- select application load balancer, name it, leave everything else the same, ensuring you have your default VPC setup. 
- Skip the warning about SSL, this is just a test app
- Create a new Security group ; open up port 80 and source 0.0.0.0/0 so anything from the outside world can access the ELB on port 80.
- Create a new target group name outyet-elb-target-group with port 80
- Register instances to your target group (registers instances to the load balancer)
Use the same subnets for your ELB as the ones you picked for the cluster

(make sure to tick all availability zones)

> what the heck did we just do?

Create a new Sec group so our EC2 instances can receive requests from the ELB
> why do we need another one? The elb's security group opens _it_ to the world, the security group we already created should be used so instances accept traffic from the load balancer

`aws ec2 authorize-security-group-ingress --group-name outyet-sg --protocol tcp --port 1-65535 --source-group outyet-elb-sg`


### create a security group, you need it for the load balancer to EC2 relationship

``aws ec2 create-security-group --group-name outyet-container-sg --description "SG so elb can talk to container`"`

- Capture the output, we'll need that (no we won't...?)

```
"GroupId": "sg-023bbd4b9f3e7b1b5"
```

- Security group is required so your app can accept incoming requests. Security groups are gate keepers for ports

The rules we've defined specify:

- Only port 80 on the ELB is exposed to the outside world.
- Any traffic from the ELB going to a container instance with the outyet-target-group group is allowed.


## Create a service that runs our task

Jump into another cloudformation template to define this service. Use the ARN (resource name) of your new load balancer's target group in this template for the target group. Again, make sure you watch the container name and exposed port.

`aws-vault exec miamollie -- aws ecs create-service --cli-input-json file://ecs-service.json`

Great! Is it working? Curl your elb. SSH into a running instance



Note to self; you had to create the ecsServiceRole yourself

https://acloud.guru/forums/aws-ecs-scaling-docker/discussion/-KoftNn-sf4KhPe6t0WZ/error%20with%20the%20service%20role
https://docs.aws.amazon.com/AmazonECS/latest/developerguide/check-service-role.html