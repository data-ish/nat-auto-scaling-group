# Nat Instance Auto Scaling Group

## Introduction
This code repository is in support of a [blog post](https://blog.data-ish.info/general/nat-auto-scaling-group.html) about NAT Gateways & Instances.  


NAT gateways are expensive. NAT instances are not highly available. This repo contains a lab example demonstrating how to make a "more" available NAT Instance, without greatly increasing the cost. Specifically:  

* A NAT instance is placed in an Auto Scaling Group, of size one.
* An Event being fired on EC2 instances coming online which triggers a utility lambda function.
* The function checks if the EC2 instance in question is a new NAT instance, and if it is updates the source/destination check property, along with any route tables.


## How to use
Two cloudformation templates are provided to create this example lab.  
First create the VPC (lab-nat-vpc.yml) this contains an example VPC to use in this lab.  
Then create the NAT asg (lab-nat-asg.yml) this contains the NAT auto scaling group, utility lambda functions, event bridge events and IAM to make this work.  
  
lab-nat-asg.yml imports values from lab-nat-vpc, so if you create the stacks with different names make sure you also override the default parameters with the correct values.
  
The required lambda functions are inlined in the cloudformation templates, this isn't ideal, but done here to reduce the amount of different resources created.

You must already have an EC2 KeyPair to create the NAT instance, and the name of this is another parameter that must be specified.

N.B. The ImageId for the Amazon Machine Image for the NAT Instance provided is valid for the London Region. If you are creating these resources in another region you will need to override the default value for this parameter.


## Useful Commands
```
aws cloudformation create-stack --stack-name lab-nat-vpc --template-body file://lab-nat-vpc.yml

aws cloudformation create-stack --stack-name lab-nat-asg --template-body file://lab-nat-asg.yml --capabilities CAPABILITY_IAM

aws cloudformation delete-stack --stack-name lab-nat-asg
aws cloudformation delete-stack --stack-name lab-nat-vpc

```