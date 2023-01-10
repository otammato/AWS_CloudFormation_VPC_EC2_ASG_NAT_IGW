# Infrastructure as a code (IaC) 





A Cloud Formation template to create this infrastructure:

<p align="center">
  <img src="https://github.com/otammato/project3_vpc_ec2_as_nat_igw/blob/main/Screenshot 2023-01-10 at 18.21.41.png" />
</p>
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

4. "PrivateSubnet": This resource creates a private subnet inside the VPC and it is of type "AWS::EC2::Subnet". Properties include the VPC id, CidrBlock of 10.0.2.0/24 and availability zone, selected using !Select function and the !GetAZs function which returns a list of availability zones in the region. This subnet also has a tag with "Name" and "Private Subnet"

5. "PublicSubnet": This resource creates a public subnet inside the VPC, it is of the same type "AWS::EC2::Subnet" like the private subnet. Properties include MapPublicIpOnLaunch set to true, which means that instances that are launched in this subnet will automatically get assigned a public IP address. It also has similar properties like private subnet such as VPC id, CidrBlock and availability zone . The subnet also has a tag with "Name" and "Public Subnet".

6. "PublicRouteTable" : This resource creates a route table that routes traffic for the public subnet. It has the type "AWS::EC2::RouteTable" and it depends on the VPC resource.

7. "PublicRouteTableAssociation" : This resource associates the public route table with the public subnet. It has the type "AWS::EC2::SubnetRouteTableAssociation", it depends on the public subnet and public route table resources, and it has properties that include the RouteTableId and SubnetId, using the !Ref function to reference the previously created resources.

8. "PublicRoute" : This resource establishes a route for public traffic to any IPv4 address. It has the type "AWS::EC2::Route" and it depends on the PublicRouteTable and InternetGateway resources. Its properties include the DestinationCidrBlock of 0.0.0.0/0, meaning that it allows traffic from any IP address, and the GatewayId, which is the Internet Gateway, and the RouteTableId is the PublicRouteTable. The route allows incoming traffic to reach the internet via the Internet Gateway that was created earlier. It routes traffic that is destined for the Internet to the Internet Gateway.

9. "VPCSecurityGroup", with a description "Security group for VPC", associated with the VPC created earlier using the VpcId property.
The SecurityGroupIngress section is where the inbound traffic is controlled and defined by a set of rules. It has four rules, the first rule allows incoming traffic on TCP port 22 (for SSH access), the second allows incoming traffic on TCP port 80 (for HTTP access), the third one allows all incoming traffic to all TCP ports and the fourth one allows incoming traffic to all ICMP protocol, that is pings. All of these rules allows incoming traffic from any IP address (0.0.0.0/0) in CIDR notation, which means any IP address is able to connect to these ports on the instances associated with this security group.

10. "EC2 instance" in a PublicSubnet, and uses the UserData property to specify a script that installs the Apache web server. The script is passed to the instance as base64 encoded data, and is executed when the instance starts up.<br>The script first runs the command "yum update -y" which updates all the packages to the latest version. Then it runs "yum install -y httpd" to install the Apache web server. After installation, the script then updates the default index page of the server and enable and start the httpd service.

11. "Elastic IP" (EIP) resource, which assigns a static IP address to the EC2 instance. The EIP is associated with the VPC, and the InstanceId property is set to the ID of the EC2 instance created earlier, so the EIP is associated with the EC2 instance.

12. PrivateRouteTable: This resource creates a route table for the private subnet. The route table is associated with the VPC specified in the VpcId property.

13. PrivateRTAssociation: This resource associates the private route table with the private subnet. The RouteTableId property is set to the ID of the private route table created earlier, and the SubnetId property is set to the ID of the private subnet.

14. ElasticIPforNAT: This resource creates an Elastic IP address for the NAT gateway. An Elastic IP is a static, public IPv4 address that you can allocate to your AWS account, and then associate with an instance or a network interface. The EIP is associated with the VPC and the domain is set to vpc

15. NatGateway: This resource creates a NAT gateway, which is a VPC component that enables instances in a private subnet to connect to the Internet or other AWS services, but prevent the Internet from initiating connections with those instances. The AllocationId property is set to the allocation ID of the Elastic IP created earlier, and the SubnetId property is set to the ID of the public subnet.

16. RouteNatGateway: This resource creates a route in the private route table that routes Internet-bound traffic to the NAT gateway. The DestinationCidrBlock property is set to '0.0.0.0/0' to indicate that the route applies to all IP addresses, the NatGatewayId property is set to the ID of the NAT gateway created earlier and the RouteTableId property is set to the ID of the private route table.
<br><br>In this way, all the traffic that originates from the instances in the private subnet will use the NAT Gateway and the Elastic IP to have internet access

17. EC2Instance2: This resource creates an Elastic Compute Cloud (EC2) instance, but this time it is placed in the private subnet. It specifies the instance type, image ID, subnet, security group for the instance.

18. AutoscalingGroup: This resource creates an Auto Scaling group, which automatically increases or decreases the number of instances in response to changes in demand for your application. The VPCZoneIdentifier property is set to the ID of the public subnet and the LaunchConfigurationName property is set to the name of the launch configuration resource created later. The MinSize and MaxSize properties set the minimum and maximum number of instances that should be running in the group, respectively. TargetGroupARNs property is set to the ID of the target group resource created later.

19. LaunchConfiguration: This resource creates a launch configuration, which describes all the settings for an instance when an Auto Scaling group launches it. It specifies the instance type, image ID, security group for the instance.

20. TargetGroup: This resource creates a Target Group, which is used to route traffic to one or more instances in an Auto Scaling group. It defines health check parameters for the instances, and also the protocol and port to be used. The HealthCheckIntervalSeconds, HealthCheckPath, HealthCheckProtocol, HealthyThresholdCount, UnhealthyThresholdCount, Port, Protocol and VpcId properties are set accordingly.

21. The autoscaling group, launch configuration and target group are used together to automatically increase or decrease the number of instances based on the traffic demand. The Target group is used to distribute the traffic across all the healthy instances and it ensures that the traffic is only sent to healthy instances
<br><br><br>

Here is an example of how you can use the aws cloudformation create-stack command to create a new stack named "my-stack" using a YAML-formatted CloudFormation template file located at "path/to/template.yaml".

It is important to remember that the file:// prefix is required for the --template-body option when specifying a file path.
<br><br>
<pre>aws cloudformation create-stack \
    --stack-name my-stack \
    --template-body file://path/to/template.yaml \
</pre>
<br><br>
Here is an example of command to delete a CloudFormation stack named "my-stack". The aws cloudformation delete-stack command is used to delete a specified CloudFormation stack and all its associated resources.
<br><br>
<pre>aws cloudformation delete-stack --stack-name my-stack
</pre>
<br><br>
Also, be careful while using this command, as it will permanently delete all the resources and data associated with the stack, which may cause data loss.
