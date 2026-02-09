provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "demo-ec2" {
  ami           = "ami-019715e0d74f695be"
  instance_type = "t3.micro"
  key_name      = "siddhesh-key-pair"

  vpc_security_group_ids = ["sg-05c75c81969af1873"]

  user_data = <<-EOF
#!/bin/bash
apt update -y
apt install -y nginx
systemctl start nginx
systemctl enable nginx
EOF

  tags = {
    Name = "MyEC2Instance"
  }
}

# Output public IP
output "ec2_public_ip" {
  value = aws_instance.demo-ec2.public_ip
}
