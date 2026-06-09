# AWS Site-to-Site VPN Architecture

> **High Availability · Secure · Scalable**

A production-ready AWS Site-to-Site VPN architecture that securely connects an on-premises corporate network to an AWS VPC using dual IPSec tunnels across two Availability Zones.

![AWS Site-to-Site VPN Architecture<img width="1344" height="896" alt="architect" src="https://github.com/user-attachments/assets/3b4be993-cfb1-46a7-aaa3-186fef28a658" />
)

---

## Overview

This architecture establishes an encrypted, highly available connection between an on-premises network (`192.168.0.0/16`) and an AWS VPC (`10.0.0.0/16`) using a Virtual Private Gateway (VGW) and a Customer Gateway (CGW), with full redundancy and a layered security model.

---

## Architecture Components

### On-Premises Side
| Component | Description |
|---|---|
| **Corporate Network** | On-premises network (`192.168.0.0/16`) |
| **Firewall / VPN Device** | Acts as the Customer Gateway (CGW) |
| **Local Network** | Workstations and servers (`192.168.0.0/16`) |

### VPN Connectivity
| Component | Description |
|---|---|
| **Customer Gateway (CGW)** | On-prem endpoint of the VPN connection |
| **Virtual Private Gateway (VGW)** | AWS-side endpoint attached to the VPC |
| **IPSec Tunnel 1** | Active tunnel — primary traffic path |
| **IPSec Tunnel 2** | Standby tunnel — automatic failover |

### VPC Layout (`10.0.0.0/16`)

The VPC is spread across **two Availability Zones** for high availability:

| Subnet | AZ A | AZ B |
|---|---|---|
| **Public Subnet** | `10.0.1.0/24` | `10.0.2.0/24` |
| **Private Subnet** | `10.0.11.0/24` | `10.0.12.0/24` |
| **Database Subnet** | `10.0.21.0/24` | `10.0.22.0/24` |

Each Availability Zone contains:
- A **NAT Gateway** in the public subnet for outbound internet access
- **App Servers** in the private subnet
- An **RDS Database** in the isolated database subnet

### Route Table

| Destination | Target |
|---|---|
| `192.168.0.0/16` | VGW (on-premises traffic) |
| `10.0.0.0/16` | Local (intra-VPC traffic) |

---

## AWS Management & Monitoring Services

| Service | Purpose |
|---|---|
| **CloudWatch** | Infrastructure monitoring |
| **CloudWatch Logs** | Centralized logging |
| **AWS IAM** | Access control & permissions |
| **AWS KMS** | Encryption key management |
| **AWS Systems Manager** | Automation & patch management |
| **S3 Bucket** | Backup storage & log archiving |

---

## Key Benefits

- **High Availability** — Two IPSec tunnels provide automatic failover with no manual intervention
- **Secure** — IPSec VPN with strong encryption, KMS-managed keys, and IAM-enforced access
- **Scalable** — VPC design supports connecting multiple VPCs and expanding CIDR ranges
- **Cost Effective** — Built-in HA at no extra cost using AWS-native capabilities
- **Fully Managed** — AWS handles the underlying VPN infrastructure

---

## Getting Started

### Prerequisites
- An AWS account with permissions to create VPC, VGW, and VPN resources
- An on-premises VPN-capable device (Cisco, Palo Alto, pfSense, etc.)
- The public IP address of your Customer Gateway device

### High-Level Deployment Steps

1. **Create a VPC** with the CIDR block `10.0.0.0/16`
2. **Create subnets** across two Availability Zones (public, private, database)
3. **Create a Virtual Private Gateway (VGW)** and attach it to the VPC
4. **Create a Customer Gateway (CGW)** using your on-prem device's public IP
5. **Create a Site-to-Site VPN connection** between the VGW and CGW
6. **Download the VPN configuration** from the AWS Console and apply it to your on-prem device
7. **Update route tables** to route on-prem traffic (`192.168.0.0/16`) through the VGW
8. **Deploy NAT Gateways, App Servers, and RDS** in the appropriate subnets
9. **Configure CloudWatch, IAM, KMS, and Systems Manager** for monitoring and security

---

## Security Considerations

- All traffic between on-premises and AWS is encrypted using IPSec
- App servers and databases are placed in private subnets with no direct internet exposure
- Database subnets are isolated from public traffic with strict security group rules
- KMS is used for encrypting data at rest (RDS, S3)
- IAM roles follow the principle of least privilege

---

## License

This project is open source and available under the [MIT License](LICENSE).
