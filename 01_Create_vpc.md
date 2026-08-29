
This terraform script creates a vpc, with 2 subnets, a public and a private, creates an internet gateway and routing tables that will give the vpc and the public subnet a route to the internet

```

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~>5.0"
    }

  }
}

# Provide the region
provider "aws" {
  region = "eu-west-1"
}

# Give the resource a name and cidr block
resource "aws_vpc" "demo_vpc" {
  cidr_block = "10.0.0.0/16"
}
# Create a public subnet
resource "aws_subnet" "public_subnet" {
  vpc_id     = aws_vpc.demo_vpc.id
  cidr_block = "10.0.0.0/24"
}
# Create a private subnet
resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.demo_vpc.id
  cidr_block = "10.0.1.0/24"
}

# Create an internet gateway that will allow the vpc to reach the internet
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.demo_vpc.id
}
# Create a default route in the routing table to the internet and associate the route with the public subnet
resource "aws_route_table" "public_rtb" {
  vpc_id = aws_vpc.demo_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}
resource "aws_route_table_association" "public_subnet" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rtb.id
}
```
