# AWS Three-Tier Architecture: Networking and Security

## Key Points

### Manual Setup
- Initial setup will be done manually to understand the process.
- Manual setup may have errors, but they can be fixed.

### Terraform Automation
- Later, you will learn how to use Terraform for the same setup.
- Emphasizes the benefits of Infrastructure as Code (IaC) for reducing human error and increasing efficiency.

### Components to Create
- **VPC (Virtual Private Cloud)**
- **Subnets**
- **Route Tables**
- **Internet Gateway**
- **NAT Gateway**
- **Security Groups**

### Error Handling
- Mistakes might happen during manual setup.
- Errors will be identified and corrected.

### Benefits of Automation
- Automating setup with Terraform ensures consistency and quick updates.
- Easier to manage and modify infrastructure through code.

## Step-by-Step Guide

### Step 1: Create VPC

#### Instructions:
1. **Login to AWS Console:**
   - Navigate to the VPC Dashboard.

2. **Create VPC:**
   - Click on **Create VPC**.
   - **Name:** `edmunds-vpc`
   - **IPv4 CIDR Block:** `10.0.0.0/16` (as per documentation)
   - Click **Create VPC**.

#### Note:
Before creating subnets, review the architectural diagram: 2 Availability Zones (AZ1 & AZ2), each with 3 subnets (2 private, 1 public).

![Architecture Diagram](https://github.com/Bathalapalli-SaiRangaPavan/Project-AWS-Three-Tier-Architecture/assets/121741348/61fe8ad2-918d-46b0-9184-4932a35a0678)

### Step 2: Create Subnets
We will create a total of 6 subnets: 2 public and 4 private.

#### Instructions:
1. **Choose the Created VPC:**
   - Navigate to the Subnets section within the VPC Dashboard.
   - Click on **Create Subnet**.

2. **Add Subnets:**
   - **Subnet Name:** `Public-Web-Subnet-AZ-1`
     - **Availability Zone:** `us-east-1a`
     - **Subnet CIDR Block:** `10.0.0.0/24`
   - Click **Add Subnet**.
   
   - **Subnet Name:** `Private-App-Subnet-AZ-1`
     - **Availability Zone:** `us-east-1a`
     - **Subnet CIDR Block:** `10.0.1.0/24`
   - Click **Add Subnet**.

   - **Subnet Name:** `Private-DB-Subnet-AZ-1`
     - **Availability Zone:** `us-east-1a`
     - **Subnet CIDR Block:** `10.0.2.0/24`
   - Click **Add Subnet**.

   - **Subnet Name:** `Public-Web-Subnet-AZ-2`
     - **Availability Zone:** `us-east-1b`
     - **Subnet CIDR Block:** `10.0.3.0/24`
   - Click **Add Subnet**.

   - **Subnet Name:** `Private-App-Subnet-AZ-2`
     - **Availability Zone:** `us-east-1b`
     - **Subnet CIDR Block:** `10.0.4.0/24`
   - Click **Add Subnet**.

   - **Subnet Name:** `Private-DB-Subnet-AZ-2`
     - **Availability Zone:** `us-east-1b`
     - **Subnet CIDR Block:** `10.0.5.0/24`
   - Click **Add Subnet**.

3. **Finalize Creation:**
   - After all subnets are listed, review the details to ensure accuracy.
   - Click **Create Subnets** to complete the process.

### Step 3: Create Internet Gateway and NAT Gateways

#### Purpose
- Establish internet access for both public and private subnets.
- One NAT gateway per availability zone to ensure redundancy.

#### Instructions

##### Create Internet Gateway
1. **Navigate to Internet Gateway**
   - Open the AWS Console and go to the Internet Gateway section.
   
2. **Create Internet Gateway**
   - Click **Create Internet Gateway**.
   - **Name:** `edmunds-igw` (or follow the naming convention in your documentation).
   - Click **Create Internet Gateway**.

3. **Attach Internet Gateway to VPC**
   - Select the newly created internet gateway.
   - Click **Actions** > **Attach to VPC**.
   - Select your VPC and attach the internet gateway.

##### Create NAT Gateways
1. **Navigate to NAT Gateway**
   - Open the AWS Console and go to the NAT Gateway section.

2. **Create NAT Gateway for AZ1**
   - Click **Create NAT Gateway**.
   - **Name:** `edmunds-nat-az1`.
   - **Subnet:** Select the `Public-Web-Subnet-AZ-1`.
   - **Elastic IP:** Allocate a new Elastic IP.
   - Click **Create NAT Gateway**.

3. **Create NAT Gateway for AZ2**
   - Repeat the process for the second NAT gateway.
   - **Name:** `edmunds-nat-az2`.
   - **Subnet:** Select the `Public-Web-Subnet-AZ-2`.
   - **Elastic IP:** Allocate a new Elastic IP.
   - Click **Create NAT Gateway**.

### Step 4: Create Route Tables

#### Public Route Table 
- **Purpose:** Route table with internet gateway for public access.
  
  **Steps:**
  1. Navigate to **Route Tables** in AWS Console.
  2. Click **Create Route Table**.
  3. Name: `edmunds-public-rt`.
  4. Select appropriate VPC.
  5. **Edit Routes:**
     - **Add Route:** Destination `0.0.0.0/0`, Target to the `Internet Gateway` attached to the VPC.
     - **Save Changes**.
  6. **Subnet Associations:**
     - **Edit Subnet Associations**.
     - Select `public-web-subnet-AZ-1` and `public-web-subnet-AZ-2` as public subnets.
     - **Save Associations**.

#### Private Route Tables
- **Purpose:** Route tables without internet gateway for private resources in AZ1 and AZ2.

  **Steps:**
  - Create two separate route tables for each Availability Zone (`edmunds-private-rt-az1` and `edmunds-private-rt-az2`).
  - Follow similar steps as above, but target the respective NAT Gateway for private subnets (`private-app-subnet-AZ-1` and `private-app-subnet-AZ-2`).

### Step 5: Create Security Groups

#### Purpose
- Create security groups to control traffic flow to different tiers and load balancers.

#### Instructions

##### Internet Facing Load Balancer Security Group
1. **Navigate to Security Groups:**
   - Open AWS Console, go to Security Groups in the EC2 Dashboard.

2. **Create Security Group:**
   - **Name:** `internet-facing-esg`
   - **Description:** Security group for internet-facing load balancer.
   - **VPC:** Select appropriate VPC.
   - **Inbound Rules:**
     - Allow HTTP (Port 80) from specific IP or IP range (e.g., your public IP).
   - Click **Create Security Group**.

##### Web Tier Security Group
1. **Create Security Group:**
   - **Name:** `web-tier-sg`
   - **Description:** Security group for web tier EC2 instances.
   - **VPC:** Select appropriate VPC.
   - **Inbound Rules:**
     - Allow HTTP (Port 80) from internet-facing security group (`internet-facing-esg`).
   - Click **Create Security Group**.

##### App Tier Security Group
1. **Create Security Group:**
   - **Name:** `app-tier-sg`
   - **Description:** Security group for application tier EC2 instances.
   - **VPC:** Select appropriate VPC.
   - **Inbound Rules:**
     - Allow custom TCP (Port 4000) from specific IP (e.g., your public IP).
     - Allow HTTP (Port 80) from web tier security group (`web-tier-sg`).
   - Click **Create Security Group**.

##### Internal Load Balancer Security Group
1. **Create Security Group:**
   - **Name:** `internal-elb-sg`
   - **Description:** Security group for internal load balancer.
   - **VPC:** Select appropriate VPC.
   - **Inbound Rules:**
     - Allow HTTP (Port 80) from app tier security group (`app-tier-sg`).
   - Click **Create Security Group**.

##### Database Tier Security Group
1. **Create Security Group:**
   - **Name:** `db-tier-sg`
   - **Description:** Security group for database tier.
   - **VPC:** Select appropriate VPC.
   - **Inbound Rules:**
     - Allow MySQL/Aurora (Port 3306) from app tier security group (`app-tier-sg`).
   - Click **Create Security Group**.

### Conclusion

By following these steps, you have successfully set up the foundational networking and security components for an AWS Three-Tier Architecture. Ensure to test each component and configuration to verify functionality.
