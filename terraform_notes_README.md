
# Terraform Notes – Infrastructure as Code (IaC)

## 1. Introduction to IaC
Infrastructure as Code (IaC) means managing and provisioning infrastructure using **code instead of manual configuration**.

### Benefits
- **Repeatable** – Same infrastructure every time
- **Automated** – Reduces manual effort
- **Version Controlled** – Stored in Git
- **Scalable** – Easy deployment across Dev, Test, Prod

Example:

```bash
terraform apply
```

---

## 2. Why We Need IaC

| Feature | Shell Script | Ansible | Terraform |
|--------|--------------|---------|----------|
| Purpose | Task automation | Config management | Infra provisioning |
| State Awareness | ❌ No | ⚠️ Limited | ✅ Yes |
| Idempotency | ❌ No | ✅ Yes | ✅ Yes |
| Cloud Support | Limited | Medium | Excellent |

**Summary**

- Shell Script → Task automation  
- Ansible → Configuration management  
- Terraform → Infrastructure provisioning  

---

## 3. Terraform Basics

Terraform uses **HCL (HashiCorp Configuration Language)**.

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

**Concepts**

- Provider → Cloud provider
- Resource → Infrastructure component
- Arguments → Resource configuration

Terraform is **declarative** – you define *what you want*.

---

## 4. Terraform Blocks

### Provider
```hcl
provider "aws" {
  region = "us-east-1"
}
```

### Resource
```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345"
  instance_type = "t2.micro"
}
```

### Variable
```hcl
variable "region" {
  default = "us-east-1"
}
```

### Output
```hcl
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```

### Module
```hcl
module "vpc" {
  source = "./modules/vpc"
}
```

### Locals
```hcl
locals {
  env = "dev"
}
```

### Data
```hcl
data "aws_ami" "latest" {
  most_recent = true
  owners = ["amazon"]
}
```

---

## 5. Terraform Installation (Ubuntu)

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install terraform
```

Check version

```bash
terraform -v
```

---

## 6. Example – Create EC2 Instance

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "MyFirstEC2"
  }
}
```

Commands

```bash
terraform init
terraform plan
terraform apply
```

---

## 7. Terraform Modules

Modules are **reusable Terraform code blocks**.

Example structure

```
terraform-project
 ├ main.tf
 ├ variables.tf
 ├ outputs.tf
 └ modules
      ├ vpc
      ├ subnet
      └ ec2
```

Benefits

- Code reuse
- Organized infrastructure
- Easy maintenance

---

## 8. Multi Environment (.tfvars)

Used for **Dev / Stage / Prod environments**.

Structure

```
ec2-multi-env
 ├ main.tf
 ├ variables.tf
 └ env
     ├ dev.tfvars
     ├ stage.tfvars
     └ prod.tfvars
```

Example

```bash
terraform apply -var-file="env/dev.tfvars"
```

---

## 9. Remote State

Terraform state file stores infrastructure info.

Production setup uses:

- **S3 bucket**
- **DynamoDB locking**

Example

```hcl
terraform {
 backend "s3" {
   bucket = "terraform-state-bucket"
   key    = "dev/terraform.tfstate"
   region = "us-east-1"
   encrypt = true
 }
}
```

---

## 10. Terraform Workspaces

Workspaces allow multiple **state files using same configuration**.

Commands

```bash
terraform workspace list
terraform workspace new dev
terraform workspace select dev
```

---

## 11. Terraform Loops

### count

```hcl
resource "aws_instance" "example" {
  count = 3
}
```

### for_each

```hcl
for_each = {
 dev = "bucket1"
 prod = "bucket2"
}
```

### for expression

```hcl
[for name in var.names : upper(name)]
```

---

## 12. Terraform Commands

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

### Taint

```bash
terraform taint aws_instance.my_instance
```

### Import

```bash
terraform import aws_instance.my_instance i-123456
```

### Target Destroy

```bash
terraform destroy -target=aws_instance.my_instance
```

---

## 13. Terraform Provisioners

Types

- local-exec
- file
- remote-exec

Example

```hcl
provisioner "local-exec" {
 command = "touch abc.txt"
}
```

⚠️ Provisioners are **not recommended for production**.

---

# Quick Interview Revision

Important topics

- IaC concept
- Terraform architecture
- Terraform blocks
- Terraform modules
- Terraform state
- Remote backend
- Terraform loops
- Terraform commands
- Workspaces vs tfvars
- Provisioners
