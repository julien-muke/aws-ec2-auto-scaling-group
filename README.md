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

To create an internet gateway:

1. Open the Amazon VPC console at https://console.aws.amazon.com/vpc/.
2. In the navigation pane, choose Internet gateways.
3. Choose Create internet gateway.
4. Enter a name for your internet gateway.

![2](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/6e011d40-6d92-4cfb-b22d-35c95f1160c9)

**To use an internet gateway, you must attach it to a VPC**

5. Choose Actions, Attach to VPC.

![3](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/d1cf899c-dbba-4bb4-aa5f-c5f0ffae75c0)

6. Select an available VPC.
7. Choose Attach internet gateway.

![4](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/472c3f64-1e75-4e3f-977d-05b6176b45d0)


## ➡️ Step 3 - Configuring Subnet

To add a subnet to your VPC:

1. Open the Amazon VPC console, in the navigation pane, choose Subnets, then choose Create subnet.
2. Under VPC ID, choose the VPC for the subnet `test-vpc`

![5 copy](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/c7fe50d2-62e9-4e1b-9d9a-7a69a1f77666)

Note: We are going to create 2 pubilc subnet in two Availability Zones: `us-east-1a` and `us-east-1b`

3. For Subnet name, enter a name for your subnet `test-public-subnet-1a`
4. Under Availability Zone, Choose the zone in which vour subnet will reside `US East (N. Virginia) / us-east-la`
5. For IPV4 subnet CIDR block, select Manual input to enter an IPV4 subnet CIDR block for your subnet `12.0.1.0/24`

![5 copy 2](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/1aa5ac4f-abdd-4f25-bf2c-f9bca180447a)


Note: To create another subnet, choose add new suhnet and repeat the same procedure mentioned above but for second Subnet name enter `test-public-subnet-1b`, for Availability Zone choose `US East (N. Virginia) / us-east-1b`, for IPv4 subnet CIDR block enter `12.0.3.0/24`. When you are done creating 2 subnets, Choose Create subnet.

**Let's Determine the route table for a subnet**

To determine the route table for a subnet:

1. Open the Amazon VPC console, on the left hand side choose Choose the Route table tab.
2. Choose Create route table
3. Enter Route table name `rt-test-public`
4. Under VPC choose `test-vpc`

![6](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/f869309c-5d4b-49d7-a687-4076b053f9b1)

**Let's associate the route table into the subnet**

1. On the route table console, choose Subnet associations tab.
2. Then choose Edit subnet associations.

![7](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/ce143d0c-cca7-4944-8b57-b5cc65e423bf)


3. Select both subnets and choose Save associations.

![8](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/72d61571-4a60-4419-89f6-be5abef1cc69)

Our route table has been associated with our subnet but this route table is also provide the internet access and that we for that we need to edit our route. 

a. On route table section, under Tab choose Routes, then edit routes.

![9](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/3c607053-a4c2-44a0-9719-07e9e161d9ab)


c. Under Edit route, choose add route
d. Enter IP address `0.0.0.0/0` which means any resources associated with route table can be accessed via internet.
e. For Target, choose Internet Gatway, then choose the internet Gateway which we have created previously `igw-test`
f. Choose Save changes

![10](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/1d8815a8-1673-4a65-962c-1246227ff69e)





