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

1. "VPC" is a resource of type "AWS::EC2::VPC", which creates a Virtual Private Cloud (VPC) in the AWS account. The VPC has a CIDR block of 10.0.0.0/16, EnableDnsSupport and EnableDnsHostnames are set to true, this means that the domain name server (DNS) hostnames are enabled for the VPC, and the VPC is configured to resolve public DNS hostnames to IP addresses.

2. "InternetGateway" is a resource of type "AWS::EC2::InternetGateway", which creates an Internet Gateway object that represents the VPC. The Internet Gateway enables communication between instances in the VPC and the Internet.

3. "VPCGatewayAttachment" is a resource of type "AWS::EC2::VPCGatewayAttachment" which connects the created Internet Gateway to the VPC. It has properties that specify the VpcId, using the !Ref function to reference the previously created VPC, and the InternetGatewayId, which also references the InternetGateway created earlier.
