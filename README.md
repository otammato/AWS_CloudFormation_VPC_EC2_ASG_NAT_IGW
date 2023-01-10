# project3





A Cloud Formation template to create this infrastructure:

![alt text](https://github.com/otammato/project3_vpc_ec2_as_nat_igw/blob/main/Project3.png?raw=true)

<br><br>


This is a CloudFormation template written in YAML. It describes the creation of several AWS resources that make up a Virtual Private Cloud (VPC) environment.


The first line of the template, "AWSTemplateFormatVersion: 2010-09-09" specifies the format version of the CloudFormation template, it's important to note that this version determines which features are available in the template and how the template is processed by CloudFormation.

Following the "AWSTemplateFormatVersion" there is the "Parameters" section, This section is used to define input parameters that can be passed to the template when it's executed. In this case, the template has two parameters: "KeyName" and "ImageID".

"KeyName" is a parameter of type "AWS::EC2::KeyPair::KeyName", which is used to specify the name of an EC2 Key Pair, which is a secure login key-pair that you can use to log in to an EC2 instance. This parameter has a ConstraintDescription of "KeyPair2" and a default value of "KeyPair2".

"ImageID" is a parameter of type "AWS::SSM::Parameter::ValueAWS::EC2::Image::Id". This parameter is used to specify the ID of the EC2 image to use when creating the EC2 instance. The default value for this parameter is "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2", which is the latest Amazon Linux AMI (Amazon Machine Image) available at the time the CloudFormation stack is executed.

the "Parameters" section sets up the parameters that are expected to be passed when the CloudFormation stack is created, these parameters will be used later on in the stack to create resources.



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
