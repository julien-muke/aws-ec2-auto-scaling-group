# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263)     How to Create an Auto Scaling Group of EC2 Instances.


## <a name="introduction">🤖 Introduction</a>

This tutorial provides a hands-on introduction to Amazon EC2 Auto Scaling through the AWS Management Console. You'll create a launch template that defines your EC2 instances and an Auto Scaling group with a single instance in it. After launching your Auto Scaling group, you'll terminate the instance and verify that the instance was removed from service and replaced. To maintain a constant number of instances, Amazon EC2 Auto Scaling detects and responds to Amazon EC2 health and reachability checks automatically.

## <a name="design">📐 Diagram Architecture</a>

![AWS EC2 Auto Scaling](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/274005ff-7950-4846-8feb-3f64f0e927d1)



## <a name="steps">☑️ Steps</a>

The procedure for deploying this architecture on AWS consists of the following steps:

Step 1. Setting up VPC (Virtual Private Cloud)

Step 2. Configuring Internet Gateway

Step 3. Configuring Subnet

Step 4. Creating a Target Group

Step 5. Setting up an Application Load Balancer

Step 6. Designing and Creating a Launch Template

Step 7. Configure Auto Scaling Group

Step 8. Testing our setup


## ➡️ Step 1 - Setting up VPC (Virtual Private Cloud)

To create a VPC, subnets, and other VPC resources using the console:

1. Open the Amazon VPC console at https://console.aws.amazon.com/vpc/
2. On the VPC dashboard, choose Create VPC.
3. For Resources to create, choose VPC only.
4. Enter the Name tag `test-vpc`
5. For IPv4 CIDR block, enter an IPv4 address range for the VPC `12.0.0.0/16`
6. Leave the rest as default, choose Create VPC.

![1](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/a4dd7f05-adc7-4a39-b03c-c464a2219888)


## ➡️ Step 2 - Configuring Internet Gateway

