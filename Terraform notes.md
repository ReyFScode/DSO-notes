# **Terraform: A Comprehensive Guide**


---
# **What it is/why we use it:**
Terraform is a powerful infrastructure as code (IaC) tool that enables users to define and provision infrastructure resources across various cloud providers. It simplifies the process of managing infrastructure by allowing you to express your desired state in a declarative manner. With Terraform, you can easily create, modify, and destroy infrastructure resources by writing code, providing a consistent and reproducible way to manage your infrastructure. 

One of the best use cases for Terraform is infrastructure provisioning and management in a cloud/multi-cloud environment. Whether you are working with AWS, Azure, GCP, or other cloud providers, Terraform allows you to define your infrastructure requirements in code, making it easy to spin up and tear down resources as needed. This makes it ideal for automating the deployment of complex architectures, including virtual machines, load balancers, databases, and networks. 

Another key use case for Terraform is that Terraform can be used to manage local VMs like Hyper-V and vSphere. Terraform provides plugins called "providers" that allow you to interact with different infrastructure platforms and services, including local infrastructure. For example, the Terraform vSphere provider allows you to manage vSphere resources, such as VMs, datacenters, and networks. Similarly, the Terraform Hyper-V provider allows you to manage Hyper-V virtual machines and other resources. By using Terraform to manage your local VMs, you can achieve the same benefits as managing cloud infrastructure with Terraform, such as version control, collaboration, and reproducibility.

- **Declarative Configuration**-
Terraform uses a declarative approach, where you define the desired state of your infrastructure in configuration files, rather than writing imperative scripts. This means you specify *what* you want, and Terraform figures out *how* to make it happen.

- **Infrastructure as Code (IaC)**-
Terraform enables you to manage your infrastructure in a code-like manner. This allows for version control, collaboration, and reproducibility of your infrastructure. Infrastructure changes become code changes, making it easier to track and manage.

- **Resource Graph**-
Terraform creates a dependency graph of the resources you define, ensuring that resources are created, updated, or destroyed in the correct order. This ensures consistency and avoids resource conflicts.

- **State Management**-
Terraform maintains a state file that records the current state of your infrastructure. This state file is used to track changes and plan updates or modifications to your infrastructure. Proper state management is crucial for Terraform's operation.




---
# **Core terraform terminology**


1.  **Provider**: a provider in terraform is a plugin that defines/manages resources for a specific infrastructure platform (e.g. AWS, Azure, VMware, etc.). 

2. **Resource:** A resource is a provider  component that you specify to create/manage/change infrastructure within the scope of the provider via Terraform (e.g. compute instances, databases, cloud networks, etc.).

3. **HCL:** stands for Hashicorp Configuration Language
 
4. **Module:** A module is a reusable/encapsulated unit of HCL code, we use modules to promote one of the core IAC pillars which is reusability/portability, we can reuse/port modules to enhance solution capabilities. Modules can be created by you or come from the Terraform Registry, which hosts community-contributed modules.

5. **outputs:** These are values created by TF after infrastructure has been created /updated. They display information about your infrastructure.

6. **State file:** This file is created by TF and keeps track of the current state of your infrastructure. This file helps Terraform/you understand what resources have been created + changes that have occurred/need to occur.

7. **Plan:** The `Terraform Plan` command/action generates a plan + allows you to preview infrastructure changes that will be applied. On the backend, once you run this command, TF will analyze configuration specifications and compare them with the current state, it will then generate output that details actions that will be taken if you apply the changes.

8. **Apply:** The `terraform apply` command/action applies changes specified in the created plan. It will create/update/delete resources depending on your configurations.

9. **Workspaces:** TF workspaces are used to manage multiple environments (Dev, testing, staging, production) via separate configs/state files. These are essential for keeping your environment infrastructure files organized.

10. **Remote backend storage:** Some form of this term is often used to describe a state file storage location that is not local, can be object storage server, cloud storage, HashiCorp TF cloud, etc.

11. **Variables:** Variables are used to parameterize your Terraform configurations. They allow you to pass values into your configuration files, making it easier to reuse and customize your infrastructure definitions.
   
   


---

# **Installing Terraform--**
To get started with Terraform, you need to install it on your local machine. Here's how (this covers downloading from .zip, there are also package based installation instructions on the terraform website)
#### 1. Download Terraform:
Visit the official Terraform website (https://www.terraform.io/downloads.html) and download the appropriate binary for your operating system (e.g., Windows, macOS, Linux).
#### 2. Install Terraform:
##### For Windows:
1. Download the zip and then extract the downloaded ZIP archive to a directory of your choice.
2. Add the directory containing the Terraform binary to your system's PATH environment variable.
##### For Linux:
1. Download the ZIP for the most recent version, you can use this command just replace the version with the most recent ver: 
``` shell
curl https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip -o /tmp/terraform.zip
```
2. Unzip the file and then move the Terraform binary to a directory included in your PATH. You can do this by running a command like `cd /tmp && unzip terraform.zip && sudo mv terraform /usr/local/bin/` on Linux.
#### 3. Verify the Installation:
Open your terminal or command prompt and run:

```shell
terraform --version
```

You should see the installed Terraform version displayed. This confirms that Terraform is installed and ready to use.




---
# **Terraform project structure**
Here's a common structure for a Terraform project (keep in mind  that **terraform projects are directory based, each directory with a main.tf is its own terraform project**, when you run the init/plan/apply terraform will look in the directory you are in for all the necessary files):

```
-terraform-project_number_1/  # top level project folder, contains all project specifc resources
├── .terraform/terraform-provider...    # generated by terraform init
├── .terraform.lock.hcl    # terraform lock file, generated by terraform init
├── main.tf                # Main configuration file (REQUIRED)
├── variables.tf           # Variable definitions
├── outputs.tf             # Output definitions
├── terraform.tfvars       # Variable values (optional)
├── terraform.tfstate      # Local state (usually .gitignore'd)
├── providers.tf           # Provider configurations
├── modules/               # Directory for reusable modules
│   ├── module1/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── module2/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
└── ...
```

- **main.tf**: The primary configuration file where you define your infrastructure resources and their relationships. All other non-auto-generated files are supplementary to this one. TL;DR this is the only required file.

- **.terraform folder / .terraform.lock.hcl**: both are auto-generated upon `terraform init`. 
	The *.terraform folder* contains a standard license.txt and is where Terraform caches provider plugins & records workspace information. 
	The *lock.hcl* file pins down the exact versions of the providers being used, this ensures that Terraform knows which dependency versions are compatible with your current setup and helps prevent issues that could arise from updates to those providers.

- **variables.tf**: This file defines input variables used in your configuration. Variables make your code more flexible and customizable.

- **outputs.tf**: Here, you define output values that you want to extract from your infrastructure after provisioning.

- **terraform.tfvars**: An optional file for setting variable values. It's useful for keeping sensitive information separate from your code.

- **terraform.tfstate**: Terraform's local state file. It keeps track of the current state of your infrastructure. It's typically ignored in version control systems.

- **providers.tf**: Configuration file for specifying provider credentials and settings.

- **modules/**: A directory for organizing reusable modules, which help keep your code DRY and promote code reuse.

---
## **Terraform State File – Why It Matters**
The `.tfstate` file is **Terraform’s memory** of what’s been deployed.

**Uses:**
- Tracks **real-world infrastructure** so Terraform can detect changes.
- Allows **efficient updates** (e.g., change only one property instead of replacing an entire resource).
- Enables **accurate dependency resolution** between resources.

Without the state file, Terraform wouldn’t know what exists or what to change.


---
# **Terraform commands**
Here are some common terraform commands that you will use to perform operations in terraform:


1. **terraform init**:
Initializes a new or existing Terraform configuration by downloading required modules, plugins, and setting up the working directory.

2. **terraform plan**:
Generates and shows the execution plan for the infrastructure. This command doesn't make changes but previews what will be created, updated, or destroyed. Best practice is to use:
`terraform plan -out generated.tfplan -no-color | tee readable.tfplan` to save the generated terraform plan file and create a readable option (no color preserves formatting). the plan file is what you apply, you can raw apply a terraform main.tf but it can cause infra drift.

3. **terraform apply**:
Applies the changes defined in the Terraform configuration to provision the infrastructure. It will ask for confirmation unless you use the `-auto-approve` flag. Normally you apply a generated plan
```bash
# flags
terraform apply -v somevar=somevalue ./plans/generated.tfplan #sets a var value

terraform apply -var-file ./some.tfvars ./plans/generated.tfplan #sets var values according to a .tfvars file (tfvars sets values via a predefined file called .tfvars, more on this in the vars section below)
```

4. **terraform destroy**:
Destroys all the resources created by your Terraform configuration. Like `apply`, it will ask for confirmation unless you use the `-auto-approve` flag.

5. **terraform show**:
Displays information about the Terraform state or the plan file.

6. **terraform output**:
Retrieves the output values defined in your Terraform configuration. You can use this to get specific values like IP addresses, instance IDs, etc.

7. **terraform validate**:
Validates the syntax and configuration of your Terraform files. This command checks whether the configuration is syntactically valid.

8. **terraform fmt**:
Formats your Terraform configuration files in a standard format for readability and consistency.

9. **terraform import**:
Imports an existing resource into Terraform's state. This command allows you to bring resources not managed by Terraform under its management.
```bash
terraform import aws_instance.my_instance i-1234567890abcdef0
```

10. **terraform state**:
Manages and inspects the state of your Terraform-managed infrastructure. Common subcommands include `list`, `show`, `mv`, and `rm`.
```bash
# View resources in the state
terraform state list

# Show detailed information for a resource
terraform state show aws_instance.my_instance
```

11. **terraform taint**:
Marks a resource for destruction and recreation on the next `terraform apply`. Useful if you want to force a resource to be replaced.
```bash
terraform taint aws_instance.my_instance
```

12. **terraform untaint**:
Removes the tainted state from a resource, meaning it will not be destroyed and recreated.
```bash
terraform untaint aws_instance.my_instance
```

13. **terraform refresh**:
Updates the Terraform state file with the current real-world status of your infrastructure without making any changes.

14. **terraform workspace**:
Manages multiple workspaces for managing different environments or deployments with the same Terraform configuration.
```bash
# Create a new workspace
terraform workspace new dev

# Switch to an existing workspace
terraform workspace select dev

# List all workspaces
terraform workspace list
```

15. **terraform graph**:
Generates a visual representation of the dependency graph of Terraform resources.

16. **terraform console**:
Launches an interactive console for evaluating expressions and querying Terraform resources.

17. **terraform state mv**:
Moves resources from one state to another, useful for renaming or reorganizing resources.
```bash
terraform state mv aws_instance.old_name aws_instance.new_name
```

18. **terraform apply -target=**:
Apply changes only to a specific resource or module.
```bash
terraform apply -target=aws_instance.my_instance
```



---

# **Terraform variables

Terraform provides a handful of ways to handle variables so that playbooks can utilize them.
*source of truth:* [Terraform Variables: A Comprehensive Guide to Dynamic Infrastructure Configuration (terrateam.io)](https://terrateam.io/blog/terraform-variables/)

### **1. Common Variable Types**
##### **1.1 Locals**
**Purpose:** Store immutable values within a Terraform configuration.  
**Definition:**

```hcl
locals {
  some_key      = "some_value"
  some_otherkey = "some_other_value"
}

Called with: "${local.some_key}"
```

**Characteristics:** Immutable (cannot change during execution), accessed with `${local.some_key}`, useful for repeated constants or computed values.

#####  **1.2 Variable Blocks**
**Purpose:** Create mutable variables that can be customized at runtime or overridden.  
**Definition:**

```hcl
variable "example_var" {
  description = "An example variable"
  type        = string
  default     = "some_value"
}

Called with: "${var.example_var}"
```

Variable blocks can be declared in `main.tf` or a dedicated `variables.tf` file. Example:
Running `terraform plan` will display the variable output for debugging.
> If a default value is set, Terraform will not prompt for input. Prompting occurs only when no default is provided.
#### **2.2 Overriding Variables**
**1. CLI Flag (`--var` / `-var`):** Highest precedence for setting variables. Overrides any default or `.tfvars` values.

```bash
terraform apply --var "test_var=new_value"
```

### **3. Variable Files**
#### **3.1 `variables.tf`**
Purpose: Declare variables for use in the main configuration via a seperate file. Keeps `main.tf` clean and organized. Example:

```hcl
variable "instance_type" {
  type        = string
  default     = "t2.micro"
  description = "Type of EC2 instance"
}

variable "region" {
  type        = string
  description = "AWS region"
}
```

#### **3.2 `.tfvars` Files**
Purpose: Assign values to variables at runtime. Example (`prod.tfvars`):
```hcl
instance_type = "t3.medium"
region        = "us-east-1"
```

Apply using:
```bash
terraform apply --var-file=prod.tfvars
```

#### 4. Summary
**Locals** → Immutable constants or computed values.  
**Variable blocks** → Mutable, overridable inputs for configurations.  
**`variables.tf`** → Variable declarations.  
**`.tfvars`** → Variable value assignments at runtime.  
Defaults prevent prompts; no default triggers input requests.  
`--var` overrides everything else.

### 5. use cases for variables in a DSO context...
#flesh_out




---


# **Resources v modules
In most production Terraform setups you’d want to use  modules, they make it easier to create multiple instances with minimal code.

### Terraform Resources
- **Definition:** The basic building blocks in Terraform configurations.
- **Purpose:** Represent individual infrastructure components managed by Terraform, such as a single AWS EC2 instance, a security group, or an S3 bucket.

- **Example:**
    ```hcl
    resource "aws_instance" "web" {
      ami           = "ami-0c55b159cbfafe1f0"
      instance_type = "t2.micro"
    }
    ```

- **Scope:** Directly declare and manage a specific piece of infrastructure
**TLDR; Resource features:**
- Full control over every argument.
- No abstraction — you see exactly what’s happening.
- Good for very simple or experimental builds.
- More boilerplate (security groups, IAM roles, EBS volumes need to be defined separately).
- No built-in best practices.
- Harder to reuse for multiple instances/environments.

### Terraform Modules
- **Definition:** Containers for multiple resources that are used together. Modules encapsulate and organize groups of resources.
- **Purpose:**
    - Promote **reusability** and **consistency** by bundling related resources into a logical unit.
    - Simplify complex configurations by abstracting away details.
    - Facilitate **sharing** and **versioning** of infrastructure code.
        
- **Example:**
    ```hcl
    module "ec2_instances" {
      source         = "terraform-aws-modules/ec2-instance/aws"
      instance_count = 3
      name           = "web-server"
      ami            = "ami-0c55b159cbfafe1f0"
      instance_type  = "t2.micro"
    }
    ```

- **Scope:** Encapsulates multiple resources and their relationships; can be nested and called from other modules or root configurations.
**TLDR; Module features:**
- Comes with best-practice defaults
- Easier to create multiple instances with minimal code.
- Outputs are already wired up (e.g., public/private IPs).
- Less granular control (although you can override a lot via variables).
- You rely on the module maintainers to update it for AWS changes.



---


# **Sample Terraform Configuration**
Here's a basic example of a Terraform main.tf configuration file that provisions an set number of ec2 instances via a module, and uses a resource to create a security group for ssh access and upload the key pair for it. Variable are provided in variables.tf

```HCL

# main.tf

provider "aws" {
  region = "us-east-1"
}

resource "aws_key_pair" "imported_key" {
  key_name   = "my-imported-key"
  public_key = "public key file contents, one line, RSA encryption"
}

resource "aws_security_group" "ssh_access" {
  name        = "allow-ssh-from-my-ip"
  description = "Allow SSH inbound traffic from my IP only"
  vpc_id      = "${var.vpc_to_target}"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["${var.my_public_ip}/32"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "my_ec2" {
  count                  = "${var.instance_count}"
  ami                    = "ami-084a7d336e816906b" # Replace if needed
  instance_type          = "t2.micro"
  subnet_id              = "${var.subnet_id}"
  key_name               = aws_key_pair.imported_key.key_name
  vpc_security_group_ids = [aws_security_group.ssh_access.id]

  tags = {
    Name = format("TEST-instance-%d", count.index + 1)
  }
}

output "instance_public_ips" {
  value = [for i in aws_instance.my_ec2 : i.public_ip]
}

#--------------------------------------------------------------------

# variables.tf


variable "instance_count" {
  description = "Number of EC2 instances"
  type        = number
  default     = 3
}

variable "my_public_ip" {
  description = "public IP for ssh rule"
  type        = string
  default     = "10.1.20.45"
}

variable "vpc_to_target" {
  description = "VPC ID"
  type        = string
  default     = "VPC ID here"
}

variable "subnet_id" {
  description = "Subnet ID to launch instances into"
  type        = string
  default     = "SUBNET ID TO LAUNCH INSTANCES INTO"
}

}
```
In this example:
- **aws_key_pair.imported_key** → Registers your **local public key** in AWS so EC2 instances can use it for SSH access.

- **aws_security_group.ssh_access** → Creates a security group that:
    - Allows SSH (`tcp` port 22) only from your given IP (`var.my_public_ip`)
    - Allows all outbound traffic

- **aws_instance.my_ec2** → Launches `var.instance_count` EC2 instances with:
    - The registered SSH key
    - The above security group
    - The chosen subnet

- **Output** → Prints the public IPs of all created instances.

to apply we run:
```
terraform init > intializes tf project

terraform plan -out generated.tfplan -no-color | tee /readable.tfplan
^ saves our plan to a generated file we can apply from, and the output to a readable plan we can reference

terraform apply ./generated.tfplan
```

---



# **Rolling back changes**




---



# **Terraform logic**
...




---


# **Using Terraform in offline environments**
By default, Terraform downloads providers from the internet when you initialize a Terraform configuration. However, it is possible to work with Terraform in an airgapped or offline environment by manually installing providers locally.

To use providers without an internet connection, you can follow these steps:

1. Download Provider Binary: Obtain the provider binary file (.exe or other format) for the specific provider you need. You can download the provider binary from the provider's official website or other trusted sources.

2. Place Provider Binary: Copy the provider binary file to a local directory or a specific location within your Terraform project. You can create a "providers" directory within your project and place the provider binary there.

3. Configure Provider Locally: In your Terraform configuration file, specify the local path to the provider binary using the  `local-exec`  provisioner. For example: ```
```
provider "aws" {
  region = "us-west-2"
  executable = "path/to/local/provider/binary"
}
```

Replace  `"path/to/local/provider/binary"`  with the actual path to the provider binary file on your local system.

4. Initialize Terraform: Run the  `terraform init`  command in your project directory to initialize Terraform. Terraform will detect the locally configured provider and use it for subsequent operations.

By following these steps, you can work with Terraform and its providers in an airgapped environment without relying on internet connectivity. Ensure that you have the correct provider binary version compatible with your Terraform configuration and that it is obtained from a trusted source.






---

# **Other Terraform info**
#### Terraform.rc file
A Terraform RC file, or  `terraform.rc`  file, is a configuration file that allows you to set default options and behaviors for the Terraform CLI (Command-Line Interface). It helps you define persistent CLI settings, such as environment variables and CLI configuration flags, which are automatically loaded when you run Terraform commands.

The  `terraform.rc`  file is typically placed in the user's home directory (e.g.,  `~/.terraformrc`  on Unix-based systems or  `%APPDATA%\terraform.rc`  on Windows). It uses the HCL (HashiCorp Configuration Language) syntax to define the configuration options.

With the  `terraform.rc`  file, you can set various configurations, such as default provider configurations, backend configurations, and environment variables. It allows you to customize and streamline your Terraform workflow by avoiding repetitive command-line options or environment variable settings.

Here's an example of a  `terraform.rc`  file:

```
provider_installation {
  filesystem_mirror {
    path    = "/path/to/provider/mirror"
    include = ["registry.terraform.io/*"]
  }
}

credentials "app.terraform.io" {
  token = "YOUR_API_TOKEN"
}

environment_variables {
  TF_LOG = "DEBUG"
}
```

In this example, the  `provider_installation`  block specifies a local provider mirror path. The  `credentials`  block sets an API token for the  `app.terraform.io`  registry. The  `environment_variables`  block sets the  `TF_LOG`  environment variable to  `DEBUG`  for verbose logging.

Note that the specific configuration options and syntax may vary depending on the Terraform version you are using. It's recommended to refer to the official Terraform documentation for the version you are working with to understand the available configurations and their usage.

...in progress