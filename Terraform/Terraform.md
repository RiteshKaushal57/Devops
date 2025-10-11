## What is IaC?
Infrastructure as Code means setting up and managing infrastructure using code instead of doing it by hand. The reason it’s used is because it saves a lot of time, reduces mistakes, and keeps all environments consistent. It works by writing a configuration file that describes what kind of setup you want — like your servers or databases — and tools such as Terraform automatically create that setup for you. So, it makes managing infrastructure as easy and reliable as writing software code.

## What is Terraform?
Terraform is a tool that allows you to manage infrastructure using code rather than doing everything manually. It’s used because it automates the setup, reduces human errors, and ensures that the same infrastructure can be recreated easily across different environments. It works by writing configuration files that describe what resources you need, and Terraform reads those files to automatically create, update, or delete the infrastructure as needed. This makes managing cloud resources fast, consistent, and reliable.

## What are Providers?
In Terraform, providers are like plugins that let Terraform connect and work with different platforms such as AWS, Azure, or Google Cloud. We use providers because Terraform itself can’t directly communicate with these platforms — the provider acts as a bridge that tells Terraform how to create and manage resources there. It works by declaring the provider in the configuration file, and when we run terraform init, Terraform automatically downloads and sets up that provider. After that, it can use the provider to manage the resources we define in our code.
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami = "ami-0123456789abcdef0" # Change the AMI 
  instance_type = "t2.micro"
}
```

## What is Multi Region?
In Terraform, multi-region means setting up your infrastructure in more than one cloud region, like having some servers in Mumbai and others in Singapore. It’s done to make your system more reliable and faster — if one region fails, the other keeps running, and users from different places get better performance. It works by defining multiple provider blocks in Terraform, each pointing to a different region. Terraform then uses those providers to create resources across all the regions you’ve mentioned in your configuration.
```
provider "aws" {
  alias = "us-east-1"
  region = "us-east-1"
}

provider "aws" {
  alias = "us-west-2"
  region = "us-west-2"
}

resource "aws_instance" "example" {
  ami = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
  provider = "aws.us-east-1"
}

resource "aws_instance" "example2" {
  ami = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
  provider = "aws.us-west-2"
}
```

## What is  Multi Cloud?
Multi-cloud means using more than one cloud platform at the same time — like combining AWS, Azure, and Google Cloud based on your needs. It’s done mainly to avoid relying on a single provider, improve reliability, and make use of each cloud’s strengths. In Terraform, this is very easy to manage because you can define multiple providers in the same project, and Terraform will automatically create and manage resources across all those clouds using one set of configuration files. This gives full control over different cloud environments from a single tool.
```
provider "aws" {
  region = "us-east-1"
}

provider "azurerm" {
  subscription_id = "your-azure-subscription-id"
  client_id = "your-azure-client-id"
  client_secret = "your-azure-client-secret"
  tenant_id = "your-azure-tenant-id"
}
```
```
resource "aws_instance" "example" {
  ami = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
}

resource "azurerm_virtual_machine" "example" {
  name = "example-vm"
  location = "eastus"
  size = "Standard_A1"
}
```
## What are variables?
In Terraform, variables are used to store values like region names, instance types, or project names so that we don’t have to repeat them everywhere in the code. They make our configuration flexible and easier to maintain. For example, if we want to change a region or instance type, we just update the variable once instead of editing multiple files. We usually define variables in a variables.tf file and assign values in a terraform.tfvars file or from the command line. This helps keep the code clean, reusable, and easy to manage across different environments.

## What are conditions?
Conditions in Terraform are used to make decisions within our code, similar to an if–else statement. They help us choose different values or configurations based on certain situations. For example, we can say — if the environment is production, use a bigger instance type; otherwise, use a smaller one. Terraform uses a simple ternary operator to do this, which looks like a short if–else expression. This makes the setup smarter, because Terraform automatically adjusts the configuration based on the condition we define.