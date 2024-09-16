to use arm arch in ec2 you must choose gravitron based instance types (t4g...)



---


# Key AWS terms




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







]


---

# Common AWS resources
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a :**






---

# AWS VPCs, an overview and how to create
**What it is:** The AWS Command Line Interface (CLI) is a unified tool that provides a command-line interface for managing your Amazon Web Services (AWS) resources. It enables you to interact with AWS services via scripts or commands from 





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

