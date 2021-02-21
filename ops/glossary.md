# Glossary

- _AWS_ is infrastructure as a service (plus other stuff)
- _VPC_ Virtual Private Cloud. A virtual networking environment, isolated section of your AWS services that keeps your stuff contained .
- _ECR_ A docker container registry [https://docs.aws.amazon.com/AmazonECR/latest/userguide/index.html](https://docs.aws.amazon.com/AmazonECR/latest/userguide/index.html)
- _Auto scaling group_ is a web service that enables you to automatically scale out or in your instances based on user-defined policies, health status checks, and schedules. Auto Scaling manages the number of EC2 instances available to handle the load for your application. Specify a min and mask, and it will ensure you’ve always got the right amount. [What is Amazon EC2 Auto Scaling? - Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) An Auto Scaling group uses a launch configuration to create instances.
- _EC2_ This provides scalable computing capacity. It eliminates your need to invest in hardware up front, so you can develop and deploy applications faster _ read” amazon servers”_ [https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html ](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- _IAM_ [https://docs.aws.amazon.com/iam/?id=docs_gateway](https://docs.aws.amazon.com/iam/?id=docs_gateway) is permissions on AWS. IAM is a web service that helps you securely control access to AWS resources for your users. Use IAM to control who can use your AWS resources (authentication) and what resources they can use in which ways (authorization).
- _Elb_ [https://docs.aws.amazon.com/elasticloadbalancing/?id=docs_gateway](https://docs.aws.amazon.com/elasticloadbalancing/?id=docs_gateway) is a load balancer, traffic from the load balancer is distributed across the instances registered to the load balancer. The load balancer checks instances in its target groups with a health check, and then ignores unhealthy ones.
- _Security group_ is as a virtual firewall for your resources to control inbound and outbound traffic. Security groups to decide who can talk to what on what port.
- _Task_ The app/ running docker container/ task is the instantiation of a task definition within a cluster
- _Service_ launches and maintains a specified number of copies of the task definition in your cluster. The benefit of a service is that the service is in charge of restarts if the task becomes unhealthy or unexpectedly stops. The Amazon ECS task scheduler is responsible for placing tasks within your cluster. You can define a service that runs and maintains a specified number of tasks simultaneously.
- _Task definition_ is a blueprint for your application. Each time you launch a task in Amazon ECS, you specify a task definition. The service then knows which Docker image to use for containers, how many containers to use in the task, and the resource allocation for each container.
- _ECS cluster_ is a logical grouping of tasks or services. The container agent runs on each EC2 within a cluster. It sends information about the resource's current running tasks and resource utilization to Amazon ECS - so it can assign tasks to instances as needed (or spin up server less on fargate)
- _Subnet group_ is a logical subdivision of an IP network._AWS_ provides two types of _subnetting_ one is Public which allow the internet to access the machine and another is private which is hidden from the internet

## EC2 vs ECS vs Fargate

EC2 is scalable containers running on a server - elastic cloud compute.

ECS manages the placement of your containers across your cluster based on your resource needs. ECS can be Serverless ( fargate) or you can configure the servers in the cluster (manage your own EC2 instances)

Fargate allows you to run Docker containers without having to manage servers or clusters. You hand over control of the management of services to AWS to do what they see fit. No need to manage or configure servers.

## Not covered

These are services we do use at 99 but not covered within the scope of this workshop

- _Cloud formation_ This is a service allowing you to create repeatable infrastructure. Set up a bunch of connected AWS services in one go, provisioning and updating them in an orderly and predictable fashion. You can define clusters, task definitions, and services as entities in an AWS CloudFormation script.
- _S3_ [https://docs.aws.amazon.com/s3/?id=docs_gateway](https://docs.aws.amazon.com/s3/?id=docs_gateway) Amazon Simple Storage Service is storage for the internet. You can use Amazon S3 to store and retrieve any amount of data at any time, from anywhere on the web
- _RDS_ web service that makes it easier to set up, operate, and scale a relational database in the cloud [https://docs.aws.amazon.com/rds/?id=docs_gateway](https://docs.aws.amazon.com/rds/?id=docs_gateway)
- _Lambda_ [https://docs.aws.amazon.com/lambda/?id=docs_gateway](https://docs.aws.amazon.com/lambda/?id=docs_gateway) aws serverless functions
- _Redshift_: [https://docs.aws.amazon.com/redshift/?id=docs_gateway](https://docs.aws.amazon.com/redshift/?id=docs_gateway) data warehouse service that makes it simple and cost-effective to efficiently analyze all your data
- *Route 53*domain Name System (DNS) web service.
- _Kenesis_ [https://docs.aws.amazon.com/kinesis/?id=docs_gateway](https://docs.aws.amazon.com/kinesis/?id=docs_gateway) data streams
- _Elasticsearch_ [https://docs.aws.amazon.com/elasticsearch-service/?id=docs_gateway](https://docs.aws.amazon.com/elasticsearch-service/?id=docs_gateway) aws hosted version of this search service
- _API Gateway_: [https://docs.aws.amazon.com/apigateway/?id=docs_gateway](https://docs.aws.amazon.com/apigateway/?id=docs_gateway) create robust, secure, and scalable APIs that access AWS or other web services, as well as data that’s stored in the AWS Cloud
  VPC [https://docs.aws.amazon.com/vpc/?id=docs_gateway](https://docs.aws.amazon.com/vpc/?id=docs_gateway)
- _Instance types_ A variety of different EC2 configurations that differ in terms of CPUs, storage, performance… and of course cost! https://aws.amazon.com/ec2/instance-types/; we use mostly T3. [T3 instances](https://aws.amazon.com/ec2/instance-types/t3/) are the next generation burstable general-purpose instance type that provide a baseline level of CPU performance with the ability to burst CPU usage at any time for as long as required
- _Availability zone_ An Availability Zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region.
- _ARN_ amazon resource name: uniquely identify AWS resources. We require an ARN when you need to specify a resource unambiguously across all of AWS, such as in IAM policies, Amazon Relational Database Service (Amazon RDS) tags, and API calls.

### References

Below are some references used to compile this workshop

[Gateway](https://docs.aws.amazon.com/ecs/?id=docs_gateway)  

[ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)

[What is fargate?](https://docs.aws.amazon.com/AmazonECS/latest/userguide/what-is-fargate.html)

[Aws docs](https://docs.aws.amazon.com/index.html)

[Circle CI - how to deploy a simple go app](https://circleci.com/blog/use-circleci-orbs-to-build-test-and-deploy-a-simple-go-application-to-aws-ecs/)

[AWS in plain English](https://expeditedsecurity.com/aws-in-plain-english/)

[Deploying docker containers in AWS](https://aws.amazon.com/getting-started/hands-on/deploy-docker-containers/)

[DevOps Girls Cloud netowrking](https://github.com/DevOps-Girls/DevOps-Girls-Cloud-Networking/blob/master/2-AWS%20Resources/aws-resources.md)
