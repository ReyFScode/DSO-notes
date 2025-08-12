to use arm arch in ec2 you must choose gravitron based instance types (t4g...)
[amazon web services - Cost of storing AMI - Stack Overflow](https://stackoverflow.com/questions/18650697/cost-of-storing-ami)

---

# How to use the AWS CLI
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a command-line interface for managing your Amazon Web Services (AWS) resources. It enables you to interact with AWS services via scripts or commands from your terminal.

##### **Configuration steps:**
- Download the AWS CLI from [aws](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html). Then double check that it has been properly installed by running `aws --version` from your terminal
- Navigate to your AWS IAM and generate access keys for the desired user you wish to connect as.
- Once properly installed, run the `aws configure` command from your terminal. you will be prompted to enter: **(1)** The aws public access key, **(2)** the AWS secret access key, **(3)** a region (if you're unsure go with 'us-east-1'), and **(4)** a desired output format, this most commonly is 'text' but other options are 'json' & 'table'. 
  You can redo a config by running the configure command again and entering through the fields (all your old inputs will remain) until you arrive at your desired field i.e. if you wanted to change output from text to json, you would run `aws configure` & just click enter until you arrived at output and make your desired change.
- Double check configurations are correct by running something simple like `aws s3 ls`

##### **Common AWS CLI commands:**




---

# Common AWS resources
**W...






---

# AWS VPCs, terminology + overview
**What it is:**  
VPCs (Virtual Private Clouds) are virtual networks within your cloud environment, logically isolated from other VPCs, but can be connected to create a larger, interconnected architecture.

Many AWS resources, such as EC2 instances and RDS databases, must be deployed within a VPC. Many users are unaware that even if they don't explicitly configure a VPC, they are still launching resources in one. AWS accounts are pre-configured with a default VPC in each region, allowing you to launch resources immediately, even without custom VPC configuration. However, it is often recommended to create custom VPCs for organizational needs and enhanced security. Many organizations choose to delete the default VPC to ensure stricter security practices, although this is not always required.

###### **Core VPC components/terms:**
- - **Subnets:**  
    Subnets are subdivisions of a VPC’s IP address range. They allow you to segment your network into smaller, manageable sections, such as public and private subnets. Public subnets are accessible from the internet, while private subnets are isolated from it. *Resources launched in a private subnet do not have a public IP address and cannot communicate with the internet directly. However, they CAN communicate with resources in other subnets within the same VPC* > This is how you can use a jump host to manage a private subnet.
    
- **Route Tables:**  
	Route tables contain a set of rules (routes) that determine where network traffic is directed within your VPC. Each subnet in your VPC must be associated with a route table, and the route table controls the outbound traffic for the resources in the subnet.
    
- **Internet Gateway (IGW):**  
	An internet gateway is a horizontally scaled, redundant, and highly available component that allows communication between your VPC and the internet. It enables resources in a VPC with public IP addresses to access the internet. It is essentially just a bridge to the internet and doesn't require an elastic IP (more on that later).
    
- **NAT Gateway/Instance:**  
	Network Address Translation (NAT) allows instances in a private subnet to connect to the internet or other AWS services while preventing inbound traffic from the internet. A NAT Gateway is a managed AWS service, whereas a NAT instance is a self-managed EC2 instance performing NAT functions. To effectively function it requires an assigned Elastic IP.
    
- **Security Groups:**  
	Security groups are virtual firewalls that control inbound and outbound traffic to resources in your VPC. You can specify which protocols, ports, and IP addresses are allowed to communicate with your resources. These operate at the resource level.
    
- **Network ACLs (NACLs):**  
	Network ACLs are also technically firewalls that provide an additional layer of security, operating at the subnet level. They control inbound and outbound traffic at the subnet level and act as stateless firewalls, meaning they evaluate traffic both ways independently.
    
- **VPC Peering:**  
	VPC peering allows you to connect one VPC to another privately, enabling resources in different VPCs to communicate with each other using private IP addresses.
    
- **VPC Endpoints:**  
    VPC endpoints allow you to privately connect your VPC to supported AWS services without needing an internet gateway, NAT device, or VPN connection. Traffic to AWS services stays within the AWS network.
    
- **Elastic IP (EIP):**  
    Elastic IP addresses are static IPv4 addresses that can be associated with AWS resources in your VPC, such as EC2 instances, to maintain a consistent IP address for external communication. 
    Some notes on EIP assignment: for them to be useful, they need to be associated with resources that can access the internet, such as instances in a public subnet. A NAT Instance (if you're using an instance instead of the managed NAT Gateway service) requires an Elastic IP so that it can allow instances in the private subnet to access the internet indirectly. An Internet Gateway (IGW) is just a bridge that allows traffic between the internet and instances within your public subnets. It doesn’t require an Elastic IP itself, the instances behind it must have EIPs associated though.


###### **Options for connecting to VPCs:**
There are a few ways we can configure our VPC for remote access to our private resources, the method you use is highly dependent on your requirements/VPC architecture.

1. **Jump Host (Bastion Host):**  
	A jump host, also known as a bastion host, is a server in a public subnet that is used to provide access to resources located in private subnets. It serves as a controlled access point and can be configured with strict security policies. This setup protects your infrastructure by minimizing the exposure of internal systems, while supporting various design topologies. SSH or RDP are common ways used to connect to a bastion host.
    
2. **VPN (Virtual Private Network):**  
	You can establish a VPN connection to securely connect on-premises networks or individual users to the resources in your VPC. Options include AWS Site-to-Site VPN for linking entire networks or AWS Client VPN for individual users accessing the VPC from remote locations. This method encrypts traffic and allows you to extend your private network into the cloud.
    
3. **Direct Connect:**  
	AWS Direct Connect provides a dedicated network connection between your on-premises infrastructure and AWS. It can be used to reduce latency and provide a consistent network experience compared to internet-based connections. Direct Connect is ideal for workloads that require a high level of bandwidth or low-latency access to VPC resources.
    
4. **Transit Gateway:**  
	AWS Transit Gateway is a service that simplifies network management by acting as a central hub for interconnecting multiple VPCs, on-premises networks, or VPN connections. It can scale to thousands of VPCs and works well in large, complex architectures requiring centralized management.
	
6. **SSH (Public Subnet with Security Groups/NACLs):**  
    Resources in public subnets can be accessed directly over the internet by using public IP addresses. Security groups and Network ACLs (NACLs) must be configured to control traffic flow, ensuring only authorized IPs or ranges have access.
6. **AWS instance connect console:**  
	AWS provides a dashboard console for access to compute instances [Connect using EC2 Instance Connect - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html#:~:text=To%20connect%20to%20your%20instance%20using%20the%20Amazon,EC2%20Instance%20Connect.%20For%20Username%2C%20verify%20the%20username.)


---

# AWS Security

**further clarification of the levels to vpc security security groups - NACLS, ec2 level control, etc.**

---

# AWS Databases
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a command-line interface for managing your Amazon Web Services (AWS) resources. It enables you to interact with AWS services via scripts or commands from 







---

# AWS messaging/streaming
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a command-line interface for managing your Amazon Web Services (AWS) resources. It enables you to interact with AWS services via scripts or commands from 




---

# AWS ECS, ECR, EKS
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a command-line interface for managing your Amazon Web Services (AWS) resources. It enables you to interact with AWS services via scripts or commands from 

##### For EKS please see the Kubernetes notes





---

# AWS DevOPS
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a command-line interface for managing your Amazon Web Services (AWS) resources. It enables you to interact with AWS services via scripts or commands from 








---
#Random/integrate in


//
**route 53 + certificate issuance -** 
When you register a new security certificate in certificate manager you should choose DNS validation if you haven't set up WHOIS emails for your domain, this activates your certificate via a CNAME record in route53. One thing you need to make sure of is that your NS record nameservers in your hosted zone for your registered domain align with the nameservers that are listed in the registered domain, if they don't match then your certificate will pend indefinitely even if a CNAME is created.

