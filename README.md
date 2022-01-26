# This project creates an ephemeral infrastructure in cloudformation

** NOTE **

This cloudformation template was created for a customer with specific needs, and is only a part of a larger solution, thus it might not suit you as a whole.
Still, you can modify, copy, and take parts of this template as you wish if you find some of it helpful.

## Goal:

The template's goal is to create an ephemeral infrastructure for one user at a time to use.
It creates a default user with temporary password and minimal IAM permissions. The user's goal is to upload a docker image to ECR.
A pipeline will be triggered and a task will be created automatically.

The template creates the following:

1. ECR repository with AES256 encryption
2. ECS cluster + task definition + roles + service
5. ECS autoscaling group + launch template + security group + user data that installs the ecs agent, cloudwatch agent and ssm agent and registering it to the cluster
6. code pipeline + code build + roles + cloudwatch event to trigger the pipeline when a docker image is pushed to ECR
7. IAM user + IAM policy

## Instructions:

Unless you don't configure otherwise, the template assumes that you have the following:

1. A keypair.
2. An EFS filesystem with security groups configured correctly.
You can manually attach the default security group to the ec2 instance that'll be created from this stack (less secure), or you can allow nfs from the vpc/subnet cidr only by creating a new security group and attach it to the EFS in the Network section. 

** NOTE **
* highly recommended: change the security group ingress from 0.0.0.0/0 to something more secure.

* When the template is created the image hasn't been pushed yet to ECR, thus I had to configure the desired capacity of the ECS task to 0, otherwise the        template creation will be stuck. So you have to manually increase the desired capacity of the task as you need. Once the desired capacity in 1 or above, the pipeline should work when an image is pushed to ECR.

* The template works only for one region at a time. 