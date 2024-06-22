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

Step 6. Create an Auto Scaling group using a launch template

Step 7. Testing our setup


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


## ➡️ Step 4 - Creating a Target Group

You register your targets with a target group. By default, the load balancer sends requests to registered targets using the port and protocol that you specified for the target group. You can override this port when you register each target with the target group. 

To create a target group using the console:

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/
2. On the navigation pane, under Load Balancing, choose Target Groups.
3. Choose Create target group.
4. For Choose a target type, select Instances to register targets by instance ID.

![11](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/45f69072-9770-450d-9fd5-b5171405a9c7)

5. For Target group name, type a name for the target group `tg-ec2-apache2`

![11 copy](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/f61d2896-afca-409a-9ca4-66dd8f15af23)


6. For VPC, select a virtual private cloud (VPC). Note that for IP addresses target types, the VPCs available for selection are those that support the IP address type that you chose in the previous step `test-vpc`
7. Leave the rest as default, Choose Next.
8. Under Register targets >> Available instances, there are no instances which has been created yet those instances will be created by Auto Scale policy.
9. choose Create target group.

![11 copy 2](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/fe9bc6b7-dc0a-4a84-93af-bbbd154143b4)

Now we have created the target group but there is no load balancer associated with this target group yet, we are just going to create that load balancer in the next step.


## ➡️ Step 5 - Setting up an Application Load 

To create an Application Load Balancer, you must first provide basic configuration information for your load balancer, such as a name, scheme, and IP address type. Then, you provide information about your network, and one or more listeners. A listener is a process that checks for connection requests. It is configured with a protocol and a port for connections from clients to the load balancer.

To configure your load balancer and listener using the console:

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
2. In the navigation pane, choose Load Balancers.
3. Choose Create Load Balancer.
4. Under Application Load Balancer, choose Create.
5. For Load balancer name, enter a name for your load balancer `alb-ec2-instances-with-asg`

![12](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/044fad9a-dff4-458c-adb8-1941aab903a6)

6. Under Network mapping:
<br>a. For VPC, select the VPC that you used for your EC2 instances `test-vpc`
<br>b. For Mappings, enable zones for your load balancer by selecting subnets from two or more Availability Zones `test-public-subnet-1a` and `test-public-subnet-1b` 

![12 copy](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/70a8e4b5-cf3b-482d-9563-7c05d8ab03d8)


7. For Security groups, select an existing security group, or create a new one.

Note: The security group for your load balancer must allow it to communicate with registered targets on both the listener port and the health check port. The console can create a security group for your load balancer on your behalf with rules that allow this communication. You can also create a security group and select it instead.

8. Choose create a new security group.

![12 copy 5](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/5f53b942-0c22-48d4-b9bc-1bf7a4dda065)


A security group acts as a virtual firewall for your instance to control inbound and outbound traffic. To create a new security group, complete the fields below.

9. Enter security group name `alb-sg-for-http-request`
10. Select the existing VPC `test-vpc`

![13](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/c2ed08a3-3a97-4a26-8071-c0ec386a635f)

11. For Inbound rules, choose Add rule.

A rules with source of `0.0.0.0/0` or :/0 allow all IP addresses to access your instance. We recommend setting security group rules to allow access from known IP addresses only. 

12. Choose Create security group

![13 copy](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/1a452d6f-8dc4-4956-a49c-30dbcf76a8ab)

13. Back to the Application Load Balancer, under security groups, refresh and add the new one `alb-sg-for-http-request`

![12 copy 2](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/6b6b2dc3-ec8c-4ca6-b511-8f677debf065)

14. For Listeners and routing, the default listener accepts HTTP traffic on port `80`. You can keep the default protocol and port, or choose different ones. For Default action, choose the target group that you created `tg-ec2-apache2`


![12 copy 3](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/0c532f23-85a6-4668-9f60-a2c34aeae4d6)


15. Review the load balancer configurations and make changes if needed. After you finish reviewing the configurations, choose Create load balancer.


## ➡️ Step 6 - Create an Auto Scaling group using a launch template


When you create an Auto Scaling group, you must specify the necessary information to configure the Amazon EC2 instances, the Availability Zones and VPC subnets for the instances, the desired capacity, and the minimum and maximum capacity limits.

To configure Amazon EC2 instances that are launched by your Auto Scaling group, you can specify a launch template or a launch configuration. The following procedure demonstrates how to create an Auto Scaling group using a launch template. 

To create an Auto Scaling group using a launch template (console):

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/, and choose Auto Scaling Groups from the navigation pane.
2. For Auto Scaling group name, enter a name for your Auto Scaling group `asg-ec2-instances-test-demo`
3. For Launch template, choose create a Launch template

![14](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/585610a7-4562-4578-b1d1-1ea45f5a9992)

4. Choose Create launch template. Enter a name `it-ec2-instances-apache2`

![15](https://github.com/julien-muke/aws-ec2-auto-scaling-group/assets/110755734/1e0b0f90-0e74-493a-ac63-ed926d8cd455)


5. Under Launch template contents, fill out each required field and any optional fields as needed:

<br>a. Choose the Application and OS Images (Amazon Machine Image) `Ubuntu Server`
<br>b. For Instance type, choose a single instance type that's compatible with the AMI that you specified `t2.micro`
<br>c. Key pair (login): For Key pair name, choose an existing key pair, or choose Create new key pair to create a new one.

For more information, see [Amazon EC2 key pairs and Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the Amazon EC2 User Guide.

<br>d. For Subnet choose Don't include in launch template
<br>e. For security groups choose the existing one `it-sg-ec2-Instances-apache2`












