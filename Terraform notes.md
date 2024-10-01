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
├── main.tf                  # Main configuration file (REQUIRED)
├── variables.tf          # Variable definitions
├── outputs.tf             # Output definitions
├── terraform.tfvars      # Variable values (optional)
├── terraform.tfstate     # Local state (usually .gitignore'd)
├── providers.tf          # Provider configurations
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
# **Terraform commands**
Here are some common terraform commands that you will use to perform operations in terraform:


1. **terraform init**:
Initializes a new or existing Terraform configuration by downloading required modules, plugins, and setting up the working directory.

2. **terraform plan**:
Generates and shows the execution plan for the infrastructure. This command doesn't make changes but previews what will be created, updated, or destroyed.

3. **terraform apply**:
Applies the changes defined in the Terraform configuration to provision the infrastructure. It will ask for confirmation unless you use the `-auto-approve` flag.

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
# **Sample Terraform Configuration**
Here's a basic example of a Terraform main.tf configuration file that provisions an AWS EC2 instance:

```HCL
# Specify the AWS provider and credentials
provider "aws" {
  region     = "us-east-1"
}

# Define an EC2 instance resource
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

# Output the public IP address of the EC2 instance
output "instance_public_ip" {
  value = aws_instance.example.public_ip
}
```
In this example:

- We specify the AWS provider
- We define an EC2 instance resource with its configuration.
- We create an output to display the public IP address of the instance.

To play around using this configuration, follow these steps:

1. Install Terraform on your system.
2. Install the aws cli and configure it with your access keys
3. Create a `main.tf` file with the above configuration.
4. Initialize the working directory using `terraform init`.
5. Review the planned changes using `terraform plan`.
6. Apply the configuration to create the resources with `terraform apply`.
7. Retrieve the outputs using `terraform output`.

Terraform will handle the provisioning and management of the EC2 instance based on your configuration.




---


# **Terraform variables

Terraform provides a handful of ways to handle variables so that playbooks can utilize them.
*source of truth:* [Terraform Variables: A Comprehensive Guide to Dynamic Infrastructure Configuration (terrateam.io)](https://terrateam.io/blog/terraform-variables/)


### 1) Common Variable types
The two most important variable types in terraform are:
1. **locals:** locals are simple local variables that you can assign values to e.g.
```
...
locals { 
somekey="somevalue"
someotherkey="someothervalue"
}
...
```
locals are immutable and will always resolve to the value they are assigned/ we call them with `${local.some_name}`.

2. **Variable blocks:** variable blocks are a way to create mutable variables, variables instantiated within a variable block can be highly customized, overridden, and set to take input from the user at plan/apply. more on these in the next section. we call them with `${var.some_name}`.
### 2.a) Setting up variable blocks
By declaring vars within variable blocks in the top-level of the main.tf or in the variables.tf we can set up vars that have mutable values / take input. Below is an example of how we declare a basic global variable, check its output/call it:
```HCL
provider "aws" {.....}

variable "testVar" {
  description="a test variable"
  type="string"
  default="someValue"
}
# ^ This declares a variable called 'testVar' that has a description, a type indicator, and a value (we store the value in the default field).

output "variableDebug" { value="${var.testVar}" }
# ^ We can check the value of a variable by using the output resource block with value set to the variable name, we always call variables in HCL with 'var.' prepended to the variable name. To run this resource and actually check the output we call terraform plan.
...
```

### 2.b) Working with variables defined in variable blocks

When we declare variables within variable blocks we can do two primary operations on them:

**> Overwrite values with the --var flag:** We can overwrite the value of our declared variables by using `--var name="value"` in the command line when we run terraform plan / terraform apply, variables must still be declared in a variable block, so if you have declared "somevar" with a default value of "foo" we can run `terraform plan --var somevar="bar"` to overwrite "foo" with "bar". **`--var` is the most powerful setting tool for variable blocks**, it doesn't matter if the value has a default, or is defined in a file, or expects an input... --var will set/overwrite its value.

**> Input variables:** We can set variable blocks to take input at runtime if we don't specify a default value in the variable block, The user will instead be prompted with an input field + the variable description. This will be overridden if the user specifies the variable value with the --var flag at plan/apply.
```
variable "simpleInput" {} 
# ^ the most simple way to define an input variable

variable "testVar" {
  description="Enter some value"
  type=bool
}
# ^ gets user input but has a custom prompt (description) and a typecode, both the above blocks can be skipped if --var flag is used to set value. e.g sample output upon running terraform plan:

var.simpleInput
  Enter a value: foo

var.testVar
  Enter some value
  Enter a value: bar
ERROR...a bool is required.

```

### 3) Using variable files:
Terraform offers two primary types of variables files to assist with variable storage/management, both these files are stored in the project directory alongside the main.tf file. It is important to understand the use case for both of these file types.

1) **variables.tf:** The variables.tf file is used to declare variable blocks/inputs for the main.tf, this promotes reusability & portability for your terraform projects while also keeping the main.tf cleaner.
for example:
```HCL
# variables.tf
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
The variables.tf file is parsed first and establishes a list of variables, if this is used in conjunction with only a main.tf then you will have to input values at runtime or overwrite/set values with --var. You can specify locals in this file but they will still be immutable, as far as I'm aware its best practice to keep locals in main.tf and only use variables.tf for blocks.

2) **.tfvars files:** the .tfvars file is used to PASS IN values to a run. For instance, if you have the above variables.tf you could just plan/apply the main.tf and manually input the region & use the default instance_type/override it with --vars... BUT if you wanted to specify a single list of values at runtime you can create a .tfvars file like below:
```HCL
# file called vars_for_run.tfvars
instance_type="SomeValue"
region="SomeRegion"
```
we can then call this .tfvars file at runtime with the `--var-file` flag e.g. `terraform plan/apply --var-file vars_for_run.tfvars` after we call it, it'll override block based variables in variables.tf & in main.tf with the specified values.

**summary / TL;DR:** variables.tf is for variable declaration, .tfvars are for runtime variable assignment

### 4) use cases for variables in a DSO context...
#flesh_out


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