# AWS Secure VPC Networking Architecture

## Project Overview

This project demonstrates the design and implementation of a secure, production-style AWS networking environment using Amazon VPC. The infrastructure follows AWS networking best practices by separating public and private resources, implementing secure routing, and enforcing network-level security.

The project simulates a startup environment where backend resources must remain isolated from the public internet while still being able to download operating system updates and security patches through a NAT Gateway.

---

## Project Objectives

- Design a secure AWS Virtual Private Cloud (VPC)
- Configure public and private subnets
- Deploy and configure an Internet Gateway
- Deploy and configure a NAT Gateway
- Configure Public and Private Route Tables
- Implement subnet-level security using Network ACLs (NACLs)
- Deploy EC2 instances in both public and private subnets
- Verify secure internet connectivity from a private subnet

---

## Architecture

<img width="971" height="541" alt="Final-AD drawio" src="https://github.com/user-attachments/assets/721b7e8f-e3f4-4fd5-aa2f-16477eecdd2b" />


---

# AWS Resources Created

| Resource | Name |
|----------|------|
| VPC | SM-NET-VPC |
| Public Subnet | SM-NET-PublicSubnet |
| Private Subnet | SM-NET-PrivateSubnet |
| Internet Gateway | SM-NET-IGW |
| NAT Gateway | SM-NET-NAT |
| Elastic IP | Associated with NAT Gateway |
| Public Route Table | SM-NET-PublicRT |
| Private Route Table | SM-NET-PrivateRT |
| Network ACL | SM-NET-NACL |
| Bastion Host | SM-NET-Bastion |
| Private Server | SM-NET-PrivateServer |

---

# Network Configuration

## VPC
<img width="1920" height="1080" alt="Screenshot (65)" src="https://github.com/user-attachments/assets/b4882f70-b82c-447d-9220-46dc4f76fac8" />

| Property | Value |
|----------|-------|
| CIDR Block | 10.0.0.0/16 |
| Region | Africa (Cape Town) |
| Availability Zone | af-south-1a |

---

## Public Subnet
<img width="1920" height="1080" alt="Screenshot (66)" src="https://github.com/user-attachments/assets/acc2e856-b508-45f0-ad13-9ef80e1551ed" />

| Property | Value |
|----------|-------|
| CIDR | 10.0.25.0/24 |
| Route Table | Public Route Table |
| Internet Access | Yes |

---

## Private Subnet
<img width="1920" height="1080" alt="Screenshot (67)" src="https://github.com/user-attachments/assets/1f5bea64-4483-4ab9-bd0b-b421939781fe" />

| Property | Value |
|----------|-------|
| CIDR | 10.0.50.0/24 |
| Route Table | Private Route Table |
| Internet Access | Through NAT Gateway Only |

---

# Routing Configuration

## Public Route Table
<img width="1920" height="1080" alt="Screenshot (78)" src="https://github.com/user-attachments/assets/10fafb81-0489-415b-9851-f55b66fe6919" />

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | Internet Gateway |

---

## Private Route Table
<img width="1920" height="1080" alt="Screenshot (77)" src="https://github.com/user-attachments/assets/f8e53deb-fe30-44c9-a014-af7e8a0ba223" />

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | NAT Gateway |

---

# Network Security

## Security Groups

### Bastion Host

Inbound

- SSH (22) from My IP

Outbound

- Allow All

---

### Private EC2

Inbound

- SSH (22) from Bastion Security Group

Outbound

- Allow All

---

## Network ACL

Inbound Rules
<img width="1920" height="1080" alt="Screenshot (87)" src="https://github.com/user-attachments/assets/b4ecbb9a-fb9c-41ef-9db1-dc9be4126f5a" />

| Rule | Port | Action |
|------|------|--------|
| SSH | 22 | Allow |
| HTTP | 80 | Allow |
| HTTPS | 443 | Allow |
| Ephemeral Ports | 1024-65535 | Allow |
| All Other Traffic | - | Deny |

Outbound Rules
<img width="1920" height="1080" alt="Screenshot (88)" src="https://github.com/user-attachments/assets/b732ea4e-5037-42d5-ab61-e35de8f40f87" />

| Rule | Port | Action |
|------|------|--------|
| SSH | 22 | Allow |
| HTTP | 80 | Allow |
| HTTPS | 443 | Allow |
| Ephemeral Ports | 1024-65535 | Allow |
| All Other Traffic | - | Deny |

---

# Connectivity Verification

## Bastion Host

Verified:

- Reachable from the internet using SSH.
- Assigned a public IPv4 address.
- Located inside the Public Subnet.
<img width="1920" height="1080" alt="Screenshot (73)" src="https://github.com/user-attachments/assets/b2593c18-0a72-4b5b-b041-e2f0bb28dc2e" />

---

## Private EC2

Verified:

- No public IPv4 address assigned.
- Located inside the Private Subnet.
- Accessible only through the Bastion Host.
<img width="1920" height="1080" alt="Screenshot (73)" src="https://github.com/user-attachments/assets/307d06e6-8413-4f7b-b8c8-09cd307472fb" />

---

## NAT Gateway Verification

The private EC2 successfully accessed the internet through the NAT Gateway.

Verification commands:

```bash
curl https://checkip.amazonaws.com
```

```bash
sudo dnf update
```

These commands confirmed outbound internet connectivity without exposing the private instance to inbound internet traffic.

---

# Why the Private Instance Cannot Be Accessed from the Internet

The private EC2 instance remains protected because:

- It has no public IP address.
- Its subnet routes internet-bound traffic through the NAT Gateway.
- The NAT Gateway only supports outbound connections initiated from the private subnet.
- The private instance's Security Group only allows SSH from the Bastion Host.
- The Network ACL restricts traffic according to the principle of least privilege.

---

# AWS Well-Architected Framework Alignment

## Security

- Network isolation using public and private subnets
- Least privilege Security Groups
- Custom Network ACLs
- Bastion Host administration

## Reliability

- Explicit route table configuration
- Managed NAT Gateway
- Managed Internet Gateway

## Operational Excellence

- Clear resource naming convention
- Infrastructure documentation
- Architecture diagram

## Cost Optimization

- AWS Free Tier eligible EC2 instances
- Single NAT Gateway
- Minimal networking resources

---

# Screenshots

Include the following screenshots:

- VPC Dashboard
- Public and Private Subnets
- Route Tables
- Internet Gateway
- NAT Gateway
- Network ACL
- EC2 Instances
- Bastion SSH Session
- Private EC2 SSH Session
- NAT Gateway Connectivity Test

---

# Skills Demonstrated

- Amazon VPC
- Public and Private Networking
- Internet Gateway
- NAT Gateway
- Elastic IP
- Route Tables
- Network ACLs
- Security Groups
- EC2
- SSH
- Linux Administration
- Cloud Security
- AWS Well-Architected Framework
- Network Troubleshooting

---

# Learning Outcomes

Through this project, I learned how to:

- Design secure AWS networking architectures.
- Separate public and private workloads using subnets.
- Configure secure routing using Route Tables.
- Deploy and configure Internet and NAT Gateways.
- Implement subnet-level traffic filtering using Network ACLs.
- Secure EC2 instances using Security Groups.
- Verify private internet connectivity through a NAT Gateway.
- Document cloud infrastructure using diagrams and technical documentation.
