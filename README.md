# AWS Three-Tier Architecture

## Architecture Overview 
![3TierArch](https://github.com/Bathalapalli-SaiRangaPavan/Project-AWS-Three-Tier-Architecture/assets/121741348/61fe8ad2-918d-46b0-9184-4932a35a0678)

In this architecture, a public-facing Application Load Balancer forwards client traffic to our web tier EC2 instances. The web tier is running Nginx web servers that are configured to serve a React.js website and redirect our API calls to the application tier’s internal-facing load balancer. The internal-facing load balancer then forwards that traffic to the application tier, which is written in Node.js. The application tier manipulates data in an Aurora MySQL multi-AZ database and returns it to our web tier. Load balancing, health checks, and auto-scaling groups are created at each layer to maintain the availability of this architecture.

## Project Outline

In this documentation, we will set up and deploy a three-tier architecture using AWS services. The architecture includes:

- **Web Tier**: Public-facing web servers to handle HTTP/HTTPS requests.
- **App Tier**: Application servers for processing business logic.
- **Database Tier**: Backend databases for data storage and management.

## AWS Services Used

- VPC (Virtual Private Cloud)
- Public Subnets
- Private Subnets
- Databases
- Web Servers
- Application Servers
- Elastic Load Balancers
- Internet Gateways
- Route 53 (for domain name mapping)

## Project Goals

- Set up a three-tier architecture
- Deploy an application accessible from a browser
- Demonstrate real-time data access from the database

## Requirements

- An AWS account (if you don't have one, please create it to proceed)

## Documentation

- We will be using the official AWS documentation with some deviations. Additional insights and real-world experience will be provided throughout the video sessions.
- Reference: [AWS Workshop Reference](https://catalog.us-east-1.prod.workshops.aws/workshops/85cd2bb2-7f79-4e96-bdee-8078e469752a/en-US)

## Next Steps

1. Ensure you have an AWS account.
2. Follow the documentation to set up the architecture.
