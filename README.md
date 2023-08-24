# Assignment1
Certainly, here's a description for a Terraform script that creates a Virtual Private Cloud (VPC) with one private subnet and one public subnet, without the need for a NAT gateway.
# Define the provider (AWS in this case)
provider "aws" {
  region = "us-east-1" # Change to your desired AWS region
}
# Create a VPC
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}
# Create a public subnet
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.my_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a" # Change to your desired availability zone
  map_public_ip_on_launch = true
}
# Create a private subnet
resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.2.0/24"
  availability_zone = "us-east-1a" # Change to your desired availability zone
}

# Create a security group
resource "aws_security_group" "instance_sg" {
  name_prefix = "instance-sg-"
  
  # Allow SSH inbound traffic
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Allow all outbound traffic
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Create an EC2 instance in the public subnet
resource "aws_instance" "my_instance" {
  ami           = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI ID (change to your desired AMI)
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  # Attach the security group
  security_groups = [aws_security_group.instance_sg.name]

  # Define the instance tags
  tags = {
    Name    = "MyEC2Instance"
    purpose = "Assignment"
  }

  # Create a 5GB EBS volume
  root_block_device {
    volume_size = 5
    volume_type = "gp2"
  }
}
# Output the VPC ID
output "vpc_id" {
  value = aws_vpc.my_vpc.id
}
# Output the public subnet ID
output "public_subnet_id" {
  value = aws_subnet.public_subnet.id
}
# Output the private subnet ID
output "private_subnet_id" {
  value = aws_subnet.private_subnet.id
}

# Output EC2 instance information
output "ec2_instance" {
  value = aws_instance.my_instance
}

output
``````
terraform output vpc_id
terraform output public_subnet_id
terraform output private_subnet_id
terraform output ec2_instance

