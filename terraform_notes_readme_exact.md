
Infrastructure as Code (IAC) & Terraform Basics
1. Introduction to IAC
Definition: IAC means writing code (instead of clicking manually in AWS/Azure GUI) to create and manage infrastructure like servers, networks, databases, etc.
Idea: Just like you use code to build an app, you use code to build infrastructure.
Benefits:
Repeatable → Same setup every time.
Automated → Saves manual effort.
Version-controlled → Stored in Git, so changes are trackable.
Scalable → Easy to deploy infra across multiple environments (Dev, Test, Prod).
👉 Example: Instead of manually creating an EC2 in AWS Console, you write a Terraform file and just run terraform apply.

2. Why we need IAC (Difference between Shell Script, Ansible, and IAC tools like Terraform)
Aspect	Shell Script	Ansible	IAC Tool (Terraform)
Purpose	Automates tasks (e.g., install software, copy files).	Config management + automation.	Full infra provisioning (VMs, networks, DBs).
State awareness	No state awareness → runs blindly.	Limited state tracking.	Maintains state file → knows what exists and what needs change.
Idempotency	❌ No → may create duplicates.	✅ Yes → ensures final state.	✅ Yes → ensures infrastructure matches code.
Cloud support	Not cloud-focused.	Supports cloud but mainly for config mgmt.	Designed for multi-cloud infra provisioning.
Example	Bash script: apt-get install nginx	Ansible Playbook to install Nginx	Terraform code to create EC2 + attach security group + install Nginx
👉 In short:

Shell Script = manual automation.
Ansible = config management & software deployment.
Terraform (IAC) = provisioning complete infra in a controlled, declarative way.
3. Terraform Language (Basic Syntax)
Terraform files are written in HCL (HashiCorp Configuration Language).

Example:

provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
Provider → Defines which cloud/service you are using (AWS, Azure, GCP).
Resource → Defines what infra you want (EC2, VPC, S3, etc.).
Arguments → Settings inside resources (ami, instance_type).
👉 It’s declarative → you say what you want, Terraform figures out how to do it.

4. Enlist the Blocks used in Terraform Language
Terraform has multiple blocks (building units):

provider → Defines the provider (AWS, Azure, etc.)

provider "aws" {
  region = "us-east-1"
}
resource → Defines infrastructure resources.

resource "aws_instance" "example" {
  ami           = "ami-12345"
  instance_type = "t2.micro"
}
variable → Input values (like parameters).

variable "region" {
  default = "us-east-1"
}
output → Shows values after deployment.

output "instance_ip" {
  value = aws_instance.example.public_ip
}
module → Group of Terraform files reused as a package.

module "vpc" {
  source = "./modules/vpc"
}
locals → Define local variables.

locals {
  env = "dev"
}
data → Fetch existing info (e.g., latest AMI).

data "aws_ami" "latest" {
  most_recent = true
  owners      = ["amazon"]
}
Terraform Instalation
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
Terraform file that creates an EC2 instance on AWS
# Define the AWS provider
provider "aws" {
  region = "us-east-1"   
}

# Create an EC2 instance
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0" 
  instance_type = "t2.micro"              

  tags = {
    Name = "MyFirstEC2"
  }
}
