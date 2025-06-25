# Terraform GCP Infrastructure Project

This Terraform configuration sets up a comprehensive, production-ready web application infrastructure on Google Cloud Platform (GCP). The project implements a modular architecture with auto-scaling, load balancing, database management, security features, and monitoring capabilities.

## 🏗️ Architecture Overview

The infrastructure includes:

- **Virtual Private Cloud (VPC)** with custom subnets and routing
- **Cloud SQL MySQL Database** with encryption and backup
- **Compute Engine Instance Group Manager** with auto-scaling
- **Regional Load Balancer** with SSL/TLS termination
- **Cloud Functions** for email verification
- **Cloud Storage** for function code and data
- **Cloud KMS** for encryption key management
- **Cloud DNS** for domain management
- **Pub/Sub** for asynchronous messaging
- **VPC Service Networking** for private Google access
- **Firewall Rules** for security
- **Service Accounts** with least-privilege access

## 📋 Prerequisites

Before running this Terraform configuration, ensure you have:

1. **Google Cloud Platform Account**: Active GCP account with appropriate permissions
2. **Google Cloud SDK** (recommended): For authentication and CLI operations
3. **Terraform**: Version 1.0+ installed on your local machine
4. **Service Account Key**: JSON key file with necessary GCP permissions
5. **SSL Certificate**: Valid SSL certificate and private key for HTTPS
6. **Domain**: Configured DNS zone in Cloud DNS
7. **Mailgun API Key**: For email verification functionality

## 🚀 Quick Start

### 1. Authentication Setup

```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account-key.json"
```

### 2. Clone and Initialize

```bash
git clone https://github.com/Basu-Patil/tf-gcp-infra
cd tf-gcp-infra
terraform init
```

### 3. Configure Variables

Create a `terraform.tfvars` file with your specific values:

```hcl
project_id = "your-gcp-project-id"
region = "us-west1"
mailgun_api_key = "your-mailgun-api-key"
certificate_path = "/path/to/your/certificate.crt"
private_key_path = "/path/to/your/private-key.key"

vpcs = {
  webapp-vpc = {
    vpc_name = "web-application-vpc-2"
    routing_mode = "REGIONAL"
    auto_create_subnetworks = false
    delete_default_routes_on_create = false
    subnets = {
      webapp-subnet = {
        subnet_name = "webapp"
        ip_cidr_range = "10.0.1.0/24"
      }
    }
    routes = {
      internet-gateway = {
        route_name = "internet-gateway"
        dest_range = "0.0.0.0/0"
        next_hop_gateway = "default-internet-gateway"
        route_tags = ["webapp"]
        priority = 1000
      }
    }
  }
}
```

### 4. Deploy Infrastructure

```bash
terraform plan
terraform apply
```

## 📁 Project Structure

```
tf-gcp-infra/
├── main.tf                 # Main Terraform configuration
├── variables.tf            # Root-level variables
├── providers.tf            # GCP provider configuration
├── README.md              # This file
└── modules/
    └── vpc/
        ├── main.tf        # VPC module implementation
        ├── variables.tf   # Module variables
        └── scripts/
            └── db-cred-setup.sh  # Database credential setup script
```

## 🔧 Core Components

### VPC and Networking
- **Custom VPC** with regional routing
- **Private subnets** with Google API access
- **Custom routes** for internet connectivity
- **VPC Service Networking** for private Google services
- **Firewall rules** for security (SSH blocked, HTTP allowed)

### Database Layer
- **Cloud SQL MySQL** instance with encryption
- **Automated backups** and maintenance windows
- **Private IP** connectivity
- **KMS encryption** for data at rest

### Compute Layer
- **Instance Group Manager** with auto-scaling
- **Custom machine types** (6 vCPU, 4GB RAM)
- **Health checks** for availability
- **Load balancing** with SSL termination
- **Custom startup scripts** for application deployment

### Security Features
- **Cloud KMS** key rings and crypto keys
- **Service accounts** with least-privilege access
- **IAM roles** for logging, monitoring, and Pub/Sub
- **Encrypted storage** buckets
- **SSL/TLS certificates** for HTTPS

### Application Services
- **Cloud Functions** for email verification
- **Pub/Sub topics** for asynchronous messaging
- **Cloud Storage** for function code and data
- **Cloud DNS** for domain management

## 🔐 Security Considerations

- SSH access is blocked by default
- All data is encrypted at rest using Cloud KMS
- Service accounts follow least-privilege principle
- Private IP addresses for internal communication
- VPC firewall rules restrict traffic flow

## 📊 Monitoring and Logging

- **Cloud Logging** integration for application logs
- **Cloud Monitoring** metrics collection
- **Health checks** for load balancer
- **Auto-scaling** based on CPU utilization

## 🔄 Auto-scaling Configuration

The infrastructure includes auto-scaling with:
- **Target CPU utilization**: 80%
- **Minimum replicas**: 1
- **Maximum replicas**: 10
- **Cooldown period**: 60 seconds
- **Distribution across multiple zones**

## 🌐 Load Balancing

- **Regional HTTPS load balancer**
- **SSL/TLS termination**
- **Health checks** on port 8080
- **Backend service** with instance groups
- **URL mapping** for routing

## 📧 Email Integration

- **Cloud Functions** for email verification
- **Mailgun API** integration
- **Pub/Sub** for message queuing
- **VPC Connector** for private function access

## 🗄️ Database Management

- **MySQL 8.0** with encryption
- **Automated backups** (daily)
- **Maintenance windows** (Sundays 2-6 AM)
- **Private IP** connectivity
- **Connection pooling** support

## 🔧 Troubleshooting

### Common Issues

#### Service Networking Connection Error
```
Error: Cannot modify allocated ranges in CreateConnection. Please use UpdateConnection
```

**Solution:**
```bash
gcloud beta services vpc-peerings update \
    --service=servicenetworking.googleapis.com \
    --ranges=private-ip-address \
    --network="web-application-vpc-2" \
    --project="your-project-id" \
    --force
```

#### Importing Existing Resources
```bash
# Example: Import existing Cloud Storage bucket
terraform import google_storage_bucket.my_bucket your-bucket-name
```

### Required APIs

Enable these APIs in your GCP project:
- Cloud DNS API
- Certificate Manager API
- Cloud Functions API
- Cloud KMS API
- Cloud SQL Admin API
- Pub/Sub API
- Compute Engine API
- Cloud Storage API

## 📝 Variables Reference

### Required Variables
- `project_id`: GCP project ID
- `region`: GCP region for deployment
- `mailgun_api_key`: Mailgun API key for email functionality
- `certificate_path`: Path to SSL certificate file
- `private_key_path`: Path to SSL private key file
- `vpcs`: Map of VPC configurations

### Optional Variables
- `vm_machine_type`: Compute instance type (default: "custom-6-4096")
- `storage_bucket_name`: Cloud Storage bucket name
- `cloud_function_name`: Cloud Function name
- `key_ring_name`: KMS key ring name
- `target_utilization`: Auto-scaling target (default: 0.8)

## 🧹 Cleanup

To destroy all resources:
```bash
terraform destroy
```

**Warning**: This will permanently delete all created resources including databases, storage buckets, and compute instances.

## 📄 License

This project is licensed under the MIT License.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📞 Support

For issues and questions:
- Create an issue in the GitHub repository
- Check the troubleshooting section above
- Review GCP documentation for specific services

