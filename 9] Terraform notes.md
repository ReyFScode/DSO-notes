# **Terraform: A Comprehensive Guide**


> 	NOTE: while it is encouraged to memorize as much as possible for all the notes in this pack, with terraform, because of how individualized each provider is, it is not possible to memorize all the configurations for everything. You will need to memorize how projects are structured and how to leverage logic and tricks to make powerful, reusable, and extensible terraform projects. For provider, module, and resource specific configuration options, look up the official docs and reference them.

---
# **What it is/why we use it:**
Terraform is a powerful infrastructure as code (IaC) tool that enables users to define and provision infrastructure resources across various cloud providers. It simplifies the process of managing infrastructure by allowing you to express your desired state in a declarative manner. With Terraform, you can easily create, modify, and destroy infrastructure resources by writing code, providing a consistent and reproducible way to manage your infrastructure. 

One of the best use cases for Terraform is infrastructure provisioning and management in a cloud/multi-cloud environment. Whether you are working with AWS, Azure, GCP, or other cloud providers, Terraform allows you to define your infrastructure requirements in code, making it easy to spin up and tear down resources as needed. This makes it ideal for automating the deployment of complex architectures, including virtual machines, load balancers, databases, and networks. Another key use case for Terraform is that Terraform can be used to manage local VMs like Hyper-V and vSphere. 

Terraform provides plugins called "providers" that allow you to interact with different infrastructure platforms and services, including local infrastructure. For example, the Terraform vSphere provider allows you to manage vSphere resources, similarly, the Terraform AWS provider allows you to manage EC2 instances, s3 buckets, and other resources. 


---

# **Installing Terraform**
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
# **Core terraform terminology**


1. **HCL:** stands for Hashicorp Configuration Language, the language Terraform uses for its projects.
   
2. **TF:** abbreviation for Terraform

3. **State file:** This file is created by TF and keeps track of the current state of your infrastructure. This file helps Terraform/you understand what resources have been created + changes that have occurred/need to occur.

4. **Plan:** The `Terraform Plan` command/action generates a plan + allows you to preview infrastructure changes that will be applied. On the backend, once you run this command, TF will analyze configuration specifications and compare them with the current state, it will then generate output that details actions that will be taken if you apply the changes.

5. **Apply:** The `terraform apply` command/action applies changes specified in the created plan. It will create/update/delete resources depending on your configurations.
   
6. **outputs:** These are values created by TF after infrastructure has been created /updated. They display information about your infrastructure.
   
7. **Workspaces:** TF workspaces are used to manage multiple environments (Dev, testing, staging, production) via separate configs/state files. These are essential for keeping your environment infrastructure files organized.

8. **Remote backend storage:** Some form of this term is often used to describe a state file storage location that is not local, can be object storage server, cloud storage, HashiCorp TF cloud, etc.


---
# Terraform project core components

Terraform can be a tad bit confusing without understanding the core project components in depth, so we will cover that here, from highest level to lowest level.

### (1) Providers
The provider is what we use to interact with a target platform, for example GCP, AWS, or Azure.  
This is a top-level specification, but because Terraform projects can be broken down into many files, it won’t always be specified at the top of `main.tf`. It’s common to see it placed in a separate file called `providers.tf`.

You can define multiple providers if you intend to interact with multiple platforms. For example:
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    sumologic = {
      source  = "sumologic/sumologic"
      version = "~> 2.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
  default_tags {
    tags = {
      Environment = "test"
      Service     = "some_service"
    }
  }
}
```
You’ll notice that the AWS provider has some additional configuration. This is standard. Each provider has its own set of configuration options, and you’ll need to reference the [Terraform provider registry](https://registry.terraform.io/browse/providers) when defining or customizing one.  
Also note that the `terraform {}` block defines general project-level settings (like required providers or backend config), while the `provider {}` block specifies how to authenticate and interact with that specific service.

### (2) Modules
Modules are where things can start to get a bit confusing. A module is a bundle of resources (we’ll cover what a resource is in the next section). Modules can be created by you or by others. Pre-built modules can be found in the [Terraform Registry](https://registry.terraform.io/browse/modules?product_intent=terraform).

MODULES ARE NOT PROVIDER LOCKED, while modules usually reference only one provider a module can use multiple providers, e.g. AWS & SumoLogic & Azure, if the use-case called for it

The point of modules is reusability. For example, if we have a VPC called `VPC-1` composed of several resources, we can create a module for it. That module can then be reused to spin up identical VPC environments by simply passing in different variables such as environment names, instance types, or images.

Modules don’t have to be a single file—many Terraform project structures break up modules by component.

Modules are called either by referencing a registry or git source, or by referencing a local path within your codebase. For example:
```
module "vpc" { # Implements the AWS-provided VPC module from the Terraform Registry
  source = "terraform-aws-modules/vpc/aws"
  name   = "my-vpc"
  cidr   = "10.0.0.0/16"
}

module "vpc1" { # Implements our custom VPC1 module from a local path
  source       = "../modules/vpc1"
  name         = "my-vpc-1"
  cidr         = "10.0.0.0/16"
  ec2_type     = "t2.micro"
  random_env   = "value"
}
```
Our custom module directory might look something like this:
```
/modules/vpc1
           |
           |- vpc.tf      # sets up networking
           |- iam.tf      # sets up permissions
           |- compute.tf  # sets up EC2 instances
           |- variables.tf
           |- outputs.tf
           |- providers.tf

# at its most minimal state however a main.tf could include all the resources configured by vpc, iam, and compute .tf as well as the outputs and providers, so it could just look like:
---|
   |-main.tf
   |-variables.tf
```
Modules often include `variables.tf` (to define input parameters) and `outputs.tf` (to expose values for use elsewhere). NOTE that when you implement a specific module you should look at its documentation to determine its configurability options, they will all differ.

### (3) Resources
A resource is the most minimal building block Terraform can create. It represents a single object provisioned via API interaction with the provider. Examples include an IAM policy, a VM instance, or a container.
EVERY RESOURCE IS LOCKED TO A PARTICULAR PROVIDER. A bundled collection of resources forms a module, which as stated above, isn't provider locked.

 For example, the resource below creates a single EC2 instance:
```
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  region        = "us-west-1"
}
```
Resources are declarative—Terraform ensures that the actual infrastructure matches the desired state you’ve defined here. Reference the docs for the particular resource you are implementing to determine its configurability options.



---

# Terraform project patterns in enterprise environments

Terraform is implemented with a GitOps-style approach in nearly all enterprise environments. Using Terraform without version control is nearly impossible due to the way most CI/CD pipelines or Terraform Cloud (commonly used to orchestrate and control Terraform-managed infrastructure) ingest and execute Terraform code, especially in response to repository changes. Enterprise Terraform design revolves around separation of environments, control of state, and strict change governance. Below we’ll break down common Terraform hierarchy patterns used in production environments.

### Pattern 1 - repo-per-service-main-per-environment

This hierarchy is probably the most common in enterprise environments where we have separation of environments (Dev, pre-prod, prod, etc.). Note that there isn’t a single “official” name mandated for this pattern by Terraform itself, but this pattern is extremely common in enterprise Terraform usage. 
Under this model, we have a Terraform repo per service, with each environment (e.g., dev, stage, prod) having its own main Terraform entry point (main.tf) that references shared modules but maintains isolated state files and variable sets. This allows changes to be tested and promoted through environments independently, with full control over drift and approval flow. 
Example structure:
```
/terraform
   |- environments/
   |      |- dev/
   |      |    |- main.tf
   |      |    |- variables.tf
   |      |    |- terraform.tfvars
   |      |
   |      |- stage/
   |      |    |- main.tf
   |      |    |- variables.tf
   |      |    |- terraform.tfvars
   |      |
   |      |- prod/
   |           |- main.tf
   |           |- variables.tf
   |           |- terraform.tfvars
   |
   |- modules/
          |- vpc/
          |- compute/
          |- iam/
          |- logging/
```
Each environment’s main file calls the same modules but is isolated via environment-specific variable files and separate backend configurations (e.g., S3 buckets or Terraform Cloud workspaces). 
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform workspace select dev
terraform init
terraform plan -var-file=./environments/dev/apollo_terraform.tfvars
terraform apply -var-file=./environments/dev/apollo_terraform.tfvars
```


### Pattern 1b - only tfvars per environment
A simpler variant of pattern 1 where the root module has a single main.tf, variables.tf, outputs.tf, and modules are shared, while each environment only maintains its own tfvars file (no separate main.tf per environment). 
Example structure:
```
/terraform
   |- main.tf
   |- variables.tf
   |- outputs.tf
   |- modules/
   |      |- vpc/
   |      |- compute/
   |      |- iam/
   |
   |- envs/
          |- dev.tfvars
          |- test.tfvars
          |- prod.tfvars
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform workspace select dev
terraform init
terraform plan -var-file=./envs/dev.tfvars
terraform apply -var-file=./envs/dev.tfvars
```

### Pattern 2 - workspace-per-environment

Single codebase with environment separation handled by Terraform workspaces. State and variable sets are tied to workspaces (dev, staging, prod). 
Example structure:
```
/terraform
   |- main.tf
   |- variables.tf
   |- terraform.tfvars
   |- modules/
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform workspace select staging
terraform plan -var-file=./envs/staging.tfvars
terraform apply -var-file=./envs/staging.tfvars
```

### Pattern 3 - repo-per-environment
Each environment lives in its own repository, often with separate pipelines, backends, and access permissions. 
Example structure:
```
repo: terraform-dev
repo: terraform-stage
repo: terraform-prod
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform init
terraform plan -var-file=./apollo_terraform.tfvars
terraform apply -var-file=./apollo_terraform.tfvars
```

### Pattern 4 - component-based (monorepo with stacks)
Infrastructure broken into components (networking, security, compute, observability). Each component has its own stack, lifecycle, and environment separation inside the component. 
Example structure:
```
/terraform
   |- networking/
   |- security/
   |- compute/
   |- observability/
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
cd networking/dev
terraform init
terraform plan -var-file=../../envs/dev.tfvars
terraform apply -var-file=../../envs/dev.tfvars
cd ../../compute/dev
terraform init
terraform plan -var-file=../../envs/dev.tfvars
terraform apply -var-file=../../envs/dev.tfvars
```

## Comparison table

| PATTERN NAME                                 | Structure / Key Idea                                                                                                | Terraform Cloud / CI/CD Integration                                                | Advantages                                                                     | Disadvantages                                                        |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| **Repo-per-service, main-per-environment**   | Separate repo per service, each env has its own main.tf, variables.tf, tfvars                                       | Workspace per environment optional; init, plan, apply with env-specific tfvars     | Strong environment isolation, independent pipelines, parallel execution        | Folder duplication, cross-env orchestration needed                   |
| **Only tfvars per environment (Pattern 1b)** | Single root module, shared main.tf, variables.tf, modules; envs differ only by tfvars                               | Workspace per environment optional; plan/apply with env-specific tfvars            | Minimal code duplication, easy onboarding of new environments                  | Less explicit state separation, requires careful varfile discipline  |
| **Workspace-per-environment**                | Single codebase, multiple Terraform workspaces (dev, staging, prod)                                                 | Workspace selection controls environment; single repo                              | Minimal duplication, easy to add environments                                  | Harder isolation, accidental apply possible                          |
| **Repo-per-environment**                     | Each environment in a separate repository                                                                           | Each repo linked to its own workspace in Terraform Cloud; independent pipelines    | Maximum isolation, independent approvals, ideal for regulated environments     | High maintenance overhead, requires strict module version management |
| **Component-based (monorepo with stacks)**   | Infrastructure split into components (networking, security, compute, observability) with per-component environments | Component stacks applied in order; use outputs / remote state to link dependencies | Scales well, clear separation of responsibility, parallel development possible | Complex orchestration, careful dependency management required        |

---
---
> IMPORTANT 1 - Terraform projects — when we say _project_, think of an environment folder from Hierarchy 1 above — always contain a `main.tf`.  
> Modules, however, may either have their components split into multiple files (for example `vpc.tf`, `ecs.tf`, `iam.tf`) or combined into a single `main.tf` that defines everything — but not both.
---
---
# Terraform File Types
> 	See the second to last section on this notes page for some common file snippet examples.

- **main.tf** – The primary configuration file where you define infrastructure resources and their relationships. This is the core of any Terraform project and the only file strictly required for Terraform to run. All other files exist to support or extend what’s defined here.

- **variables.tf** – Terraform is Declarative as we have established, so this file **Declares** input variables used throughout your configuration. Variables make code reusable and environment-agnostic by allowing you to pass values (for example region, environment name, or instance type) without modifying source code directly.
    Variables, unless a default value is set OR provided via CLI, are passed through:
    - **terraform.tfvars** – Provides values for the variables declared in `variables.tf`. Typically holds environment-specific or sensitive data. This file is automatically loaded if present and allows for clean separation between configuration logic and data.
    - **.auto.tfvars–** Works the same way but supports multiple files. Terraform automatically loads all files in the working directory ending with `.auto.tfvars` in alphabetical order. This allows separate varfiles per environment (for example `dev.auto.tfvars`, `prod.auto.tfvars`) without needing `-var-file` flags. The key difference: `terraform.tfvars` is a single default file, while `.auto.tfvars` is a flexible pattern-based system that supports multiple autoloaded configurations.

- **locals.tf** – Defines local variables, which are computed values derived from expressions or inputs. Locals reduce duplication and make complex configurations easier to read by centralizing computed logic.

- **data.tf** – Declares data sources that retrieve existing infrastructure or external information (for example, fetching the latest AMI ID or an existing subnet). Data blocks are read-only and do not create resources, but they’re often referenced by `main.tf` resources.

- **outputs.tf** – Defines values Terraform should display or pass to other systems after deployment. Outputs often expose resource IDs, IP addresses, or ARNs that other modules or pipelines depend on.

- **providers.tf** – Declares and configures providers if they’re not already defined in `main.tf`. Providers tell Terraform how to communicate with external platforms (AWS, Azure, GCP, etc.). Many teams keep provider settings here for clarity and to simplify upgrades.

- **backend.tf** – Defines the remote backend used to store Terraform state files (for example, S3, Terraform Cloud, or Azure Storage). This ensures centralized, consistent state management across users and pipelines.

- **versions.tf** – Specifies the required Terraform version and provider versions to maintain consistency across teams and environments. Typically includes a `terraform` block with `required_version` and `required_providers`.

- **terraform.tfstate** – Tracks the current state of your deployed infrastructure. Terraform uses this file to determine what exists and what must be created, updated, or destroyed. In enterprise environments, this file should always reside in a remote backend defined in `backend.tf`. See section below for a little more information on this file.

- **terraform.tfstate.backup** – A local backup automatically generated when the main state file changes. It provides a rollback point if the active state becomes corrupted or misapplied.

- **.terraform.lock.hcl** – A dependency lock file automatically generated by `terraform init`. It pins provider versions to ensure consistent behavior across runs and environments, preventing unexpected provider upgrades.

- **.terraformignore** – Functions like `.gitignore` for Terraform Cloud and Enterprise. It specifies which local files or directories should be excluded from upload (for example `.git/`, `*.tfstate`, `.terraform/`).

- **Terraform.rc file:** - A Terraform RC file, or  `terraform.rc`  file, helps you define persistent CLI settings, such as environment variables and CLI configuration flags, which are automatically loaded when you run Terraform commands. The  `terraform.rc`  file is typically placed in the user's home directory (e.g.,  `~/.terraformrc`  on Unix-based systems or  `%APPDATA%\terraform.rc`  on Windows). It uses the HCL (HashiCorp Configuration Language) syntax to define the configuration options. With this you can set various configurations, such as default provider configurations, backend configurations, and environment variables. It allows you to customize and streamline your Terraform workflow by avoiding repetitive command-line options or environment variable settings. [Source of truth](https://developer.hashicorp.com/terraform/cli/config/config-file)


**Directories**

- **modules/** – Organizes reusable Terraform modules. Each subdirectory defines a discrete piece of infrastructure such as networking, compute, or IAM. This structure promotes reusability and maintainability across multiple projects or environments. See Note above this section for an additional point on the files in this directory.

- **.terraform/** – Created automatically by `terraform init`. Contains cached provider plugins, module source code, and workspace metadata. It should never be modified manually or committed to version control.



---

## **Terraform State File – Why It Matters**
The `.tfstate` file is **Terraform’s memory** of what’s been deployed.

**Uses:**
- Tracks **real-world infrastructure** so Terraform can detect changes.
- Allows **efficient updates** (e.g., change only one property instead of replacing an entire resource).
- Enables **accurate dependency resolution** between resources.

Without the state file, Terraform wouldn’t know what exists or what to change.


---

# **Terraform Commands**

These are the Terraform commands you’ll use most frequently — ordered by importance and real-world usage frequency in enterprise environments.

1. **terraform init**  
    Initializes a new or existing Terraform project by downloading required provider plugins, modules, and setting up the working directory. This must be run once in each project folder (see important 1 above) before any other command after cloning or modifying a configuration.

2. **terraform plan**  
    Generates and shows the execution plan — a preview of what Terraform will create, update, or destroy. It does **not** make changes.  minimally this command is just `terraform plan` 
	but best practice is usually (when planning manually):
```bash
terraform plan -out=generated.tfplan -no-color | tee readable.tfplan
```
This saves a binary plan file (`.tfplan`) for safe apply and creates a colorless readable version for review. Always review before applying to prevent infrastructure drift.

3. **terraform apply**  
    Applies the execution plan to create or modify infrastructure. It prompts for confirmation unless `-auto-approve` is used.  When run as just `terraform apply` it will generate a plan, ask for approval, and then apply the configuration, but if you planned separately and saved the plan as a file you can apply it with:
```bash
terraform apply ./plans/generated.tfplan

# you can also overwrite / designate variables on apply with the var flag specifying a standalone variable or a vars file.
terraform apply -var="somevar=value"
terraform apply -var-file=./envs/dev.tfvars ./plans/generated.tfplan
```
Applying a saved plan ensures consistency between planning and applying phases — critical in automated pipelines. Most enterprise environments use a remote state backend (terraform cloud/s3/etc.) to aggregate and save their state files.

4. **terraform destroy**  
    Tears down all infrastructure created by the configuration. It also prompts for confirmation unless `-auto-approve` is used.  
    Used cautiously — usually in dev/test environments or teardown pipelines.

5. **terraform validate**  
    Validates syntax and internal consistency of your Terraform configuration. This checks for structural errors before running `plan`. Often automated as a pre-commit or pipeline step.

6. **terraform fmt**  
    Automatically formats Terraform code into the canonical style for readability and consistency. Run regularly or enforce it through CI to maintain clean diffs and consistent reviews.

7. **terraform show**  
    Displays details of a Terraform state file or plan file. Commonly used to inspect deployed infrastructure or review plans.
```bash
terraform show terraform.tfstate
terraform show generated.tfplan
```

8. **terraform output**  
    Retrieves the output values defined in `outputs.tf`. Often used to extract resource identifiers, IPs, or ARNs for use in pipelines or scripts.
```bash
terraform output vpc_id
terraform output -json
```

9. **terraform state**  
    Manages and inspects Terraform’s state file directly. Common subcommands include `list`, `show`, `mv`, and `rm`:
```bash
terraform state list
terraform state show aws_instance.my_instance
terraform state mv aws_instance.old aws_instance.new
```
Used cautiously — manual edits can desync real infrastructure from Terraform’s view.

10. **terraform import**  
    Brings an existing real-world resource under Terraform management without recreating it.
```bash
terraform import aws_instance.my_instance i-1234567890abcdef0
```
Critical when onboarding legacy infrastructure or transitioning to Infrastructure as Code.

11. **terraform refresh**  
    Updates the local or remote state file to reflect the current status of real infrastructure **without** modifying anything. Helps detect drift between declared and actual resources.

12. **terraform apply -target=**  
    Applies changes to a specific resource or module only.
```bash
terraform apply -target=aws_instance.my_instance
```
Useful for partial updates or dependency debugging, but not recommended as a routine workflow — it can cause long-term drift if used excessively.

13. **terraform taint**  
    Marks a resource for destruction and recreation during the next apply.
```bash
terraform taint aws_instance.my_instance
```
Used when a resource is unhealthy or misconfigured but still exists in state.

14. **terraform untaint**  
    Reverses a taint, preventing replacement on the next apply.
```bash
terraform untaint aws_instance.my_instance
```

15. **terraform workspace**  
    Manages multiple workspaces — isolated state files within the same configuration (for example dev, stage, prod).
```bash
terraform workspace new dev
terraform workspace select dev
terraform workspace list
```
Useful for lightweight environment separation when not using fully distinct backend directories.

16. **terraform graph**  
    Generates a dependency graph in DOT format, which can be rendered using tools like Graphviz to visualize relationships between resources and modules.

17. **terraform console**  
    Opens an interactive console for evaluating Terraform expressions, testing interpolations, and inspecting state data. Helpful for debugging variable or expression logic.


---

# **Terraform variables

Terraform provides a handful of ways to handle variables so that playbooks can utilize them.
*source of truth:* [Terraform Variables: A Comprehensive Guide to Dynamic Infrastructure Configuration (terrateam.io)](https://terrateam.io/blog/terraform-variables/)

### **1. Common Variable Types**

##### **1.0** **Data sources**
...
##### **1.1 Locals**
**Purpose:** Store immutable values within a Terraform configuration.  SHOULD & CAN only be put in the main.tf
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
Purpose: Declare variables for use in the main configuration via a separate file. Keeps `main.tf` clean and organized. Example:

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
> can be named `terraform.tfvars` for standard local project variable values OR can be called `*.auto.tfvars` in a production environment (common for gitops repos where we hold and version the configs for multiple environments), e.g *prod.auto.tfvars* or *dev.auto.tfvars*, or something akin to that.

Purpose: Assign values to variables at runtime. NOTE: variables to provide must exist in variables.tf file for the project ????true??? 

File Example:
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


# **Terraform cloud


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
---
# emergency help section⚠️

---
---

# **I DELETED MY STATE FILE! / MY STATE IS LOST!**
If you accidentally **delete a Terraform state file**, your configuration no longer “knows” what resources exist. This is serious, but there are ways to reconcile your state depending on your setup. 
## 1. **Check for backups first**

- Terraform automatically creates a backup of your state: `terraform.tfstate.backup` (local) or your **remote backend** (S3, Terraform Cloud, etc.).
    
- **Restore the backup** if possible — this is usually the fastest and safest approach.
    
```bash
cp terraform.tfstate.backup terraform.tfstate
```
Or, in a remote backend (Terraform Cloud/S3), just reinitialize and pull the latest state:
```bash
terraform init
terraform refresh
```


## 2. **Re-import resources**

If no backup is available, you must **reconstruct the state** using `terraform import` for each existing resource.

1. Identify the resource in your cloud provider (e.g., an AWS EC2 instance).
    
2. Run `terraform import` to add it back to the state
```bash
terraform import aws_instance.web i-1234567890abcdef0
```

- Repeat this for all resources in the environment.
    
- The resource in your `.tf` files must **match the actual existing resource** for import to succeed.
    
## 3. **Rebuild state incrementally**

- You can also use `terraform refresh` after importing some resources to reconcile the live infrastructure with your `.tf` files.
    
- This updates the state file with real-world values without changing any resources:
```bash
terraform refresh
```

## 4. **Plan and verify**

Once the state is rebuilt:
```bash
terraform plan
```

- Terraform will show what it thinks needs to be created, modified, or destroyed.
    
- Carefully verify that Terraform isn’t trying to **recreate existing resources**, which would cause duplication or conflicts.
    
## 5. **Optional: Use remote backend snapshots**

- If using Terraform Cloud or an S3 backend with versioning, you may have **older snapshots** of your state file.
    
- Restore from the snapshot instead of manual re-importing.
