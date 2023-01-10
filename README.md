# project3





A Cloud Formation template to create this infrastructure:

![alt text](https://github.com/otammato/project3_vpc_ec2_as_nat_igw/blob/main/Project3.png?raw=true)

<br><br>


This is a CloudFormation template written in YAML. It describes the creation of several AWS resources that make up a Virtual Private Cloud (VPC) environment.

First, it defines two parameters: KeyName and ImageID. KeyName is used to specify a key pair for accessing instances launched in the VPC, and ImageID is used to specify an Amazon Machine Image (AMI) for instances launched in the VPC.

The Resources section is where the main components of the VPC are defined. It creates the following resources:

1. VPC (Virtual Private Cloud): A VPC is a logically-isolated section of the AWS cloud where you can launch AWS resources in a virtual network that you've defined. In this template, it is defined with a CIDR block of 10.0.0.0/16, with both DNS support and hostnames enabled.

2. InternetGateway: An Internet Gateway is a VPC component that allows communication between instances in the VPC and the Internet. This resource is created but not configured, no properties are set on it.

3. VPCGatewayAttachment: This resource is used to connect the Internet Gateway to the VPC. It's defined with a reference to the VPC resource, and InternetGatewayId.

4. PrivateSubnet: A subnet inside the VPC. It is defined with a CIDR block of 10.0.2.0/24 and is associated with the VPC defined earlier. The PrivateSubnet also has a tag with name "Private Subnet".

5. PublicSubnet: Another subnet inside the VPC, defined with a CIDR block of 10.0.1.0/24. It is also associated with the VPC defined earlier. Public subnet has a property "MapPublicIpOnLaunch : true" which means instances in this subnet will be allocated a public IP by default. And also has a tag with name "Public Subnet"

6. PublicRouteTable: This is a route table that routes traffic to the Internet via the Internet Gateway. It is defined with a reference to the VPC resource and has a tag with name "Public Route Table".

7. PublicRouteTableAssociation: This resource associates the PublicRouteTable with the PublicSubnet. it's defined with a reference to the PublicRouteTable and PublicSubnet resources.

8. PublicRoute: This resource establishes a public route for traffic to any IPv4 address via the Internet Gateway. It's defined with a destination CIDR block of 0.0.0.0/0 and a reference to the Internet Gateway and PublicRouteTable resources.

9. SecurityGroup: This resource creates a security group that allows incoming traffic via TCP on ports 22 and 80, and all ports. The security group is associated with the VPC defined earlier, and it has a group name "VPCSecurityGroup" and a group description "Security group for VPC".

Overall, this template creates a VPC with both public and private subnets. It establishes internet connectivity for the public subnet via an Internet Gateway and associated route tables, and creates a security group to allow incoming traffic to instances launched in the VPC.
