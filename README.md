# project3





A Cloud Formation template to create this infrastructure:

![alt text](https://github.com/otammato/project3_vpc_ec2_as_nat_igw/blob/main/Project3.png?raw=true)

<br><br>


This is a CloudFormation template written in YAML. It describes the creation of several AWS resources that make up a Virtual Private Cloud (VPC) environment.

First, it defines two parameters: KeyName and ImageID. KeyName is used to specify a key pair for accessing instances launched in the VPC, and ImageID is used to specify an Amazon Machine Image (AMI) for instances launched in the VPC.

The Resources section is where the main components of the VPC are defined. It creates the following resources:

VPC: A Virtual Private Cloud, which is a logically-isolated section of the AWS Cloud where the user can launch AWS resources in a virtual network that the user has defined.

InternetGateway: This is a VPC component that allows communication between instances in the VPC and the Internet.

VPCGatewayAttachment: This is used to connect the Internet Gateway to the VPC.

PrivateSubnet and PublicSubnet: These are subnets inside the VPC which will host instances.

PublicRouteTable: This is a route table that routes traffic to the Internet via the Internet Gateway.

PublicRouteTableAssociation: This associates the PublicRouteTable with the PublicSubnet.

PublicRoute: This establishes a public route for traffic to any IPv4 address via the Internet Gateway.

SecurityGroup: This creates a security group that allows incoming traffic via TCP on ports 22 and 80, and all ports.

Overall, this template creates a VPC with both public and private subnets. It establishes internet connectivity for the public subnet via an Internet Gateway and associated route tables, and creates a security group to allow incoming traffic to instances launched in the VPC.
