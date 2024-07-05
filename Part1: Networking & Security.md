# AWS Three-Tier Architecture: Networking and Security Setup Guide

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

Before creating subnets, review the architectural diagram showing 2 Availability Zones (AZ1 & AZ2), each with 3 subnets (2 private, 1 public).
![3TierArch](https://github.com/Bathalapalli-SaiRangaPavan/Project-AWS-Three-Tier-Architecture/assets/121741348/61fe8ad2-918d-46b0-9184-4932a35a0678)



### Step 2: Create Subnets

#### Instructions:

1. **Choose the Created VPC:**
   - Navigate to the Subnets section within the VPC Dashboard.
   - Click on **Create Subnet**.

2. **Add Subnets:**

   - **Subnet Name:** `Public-Web-Subnet-AZ-1`
     - **Availability Zone:** `us-east-1a`
     - **Subnet CIDR Block:** `10.0.0.0/24`
   - Click **Add Subnet**.
   
   - Repeat for other subnets (Public and Private) in AZ1 and AZ2.

3. **Finalize Creation:**
   - Review all subnets listed.
   - Click **Create Subnets**.

### Step 3: Create Internet Gateway and NAT Gateways

#### Instructions:

#### Create Internet Gateway

1. **Navigate to Internet Gateway:**
   - Open the AWS Console and go to the Internet Gateway section.
   
2. **Create Internet Gateway:**
   - Click **Create Internet Gateway**.
   - **Name:** `edmunds-igw`.
   - Click **Create Internet Gateway**.

3. **Attach Internet Gateway to VPC:**
   - Select the newly created internet gateway.
   - Click **Actions** > **Attach to VPC**.
   - Select your VPC and attach.

#### Create NAT Gateways

1. **Navigate to NAT Gateway:**
   - Open the AWS Console and go to the NAT Gateway section.

2. **Create NAT Gateway for AZ1:**
   - Click **Create NAT Gateway**.
   - **Name:** `edmunds-nat-az1`.
   - **Subnet:** Select the `Public-Web-Subnet-AZ-1`.
   - Allocate a new Elastic IP.
   - Click **Create NAT Gateway**.

3. **Create NAT Gateway for AZ2:**
   - Repeat the process for AZ2.
   - **Name:** `edmunds-nat-az2`.
   - **Subnet:** Select the `Public-Web-Subnet-AZ-2`.
   - Allocate a new Elastic IP.
   - Click **Create NAT Gateway**.

### Step 4: Create Route Tables

#### Instructions:

#### Public Route Table

1. **Navigate to Route Tables in AWS Console:**
   - Click on **Create Route Table**.
   - **Name:** `edmunds-public-rt`.
   - Select appropriate VPC.

2. **Edit Routes:**
   - Add Route: Destination `0.0.0.0/0`, Target to the Internet Gateway attached to the VPC.
   - Save Changes.

3. **Subnet Associations:**
   - Edit Subnet Associations.
   - Select `public-web-subnet-AZ-1` and `public-web-subnet-AZ-2` as public subnets.
   - Save Associations.

#### Private Route Tables (AZ1 and AZ2)

1. **Create Private Route Table for AZ1:**
   - Click on **Create Route Table**.
   - **Name:** `edmunds-private-rt-az1`.
   - Select appropriate VPC.

2. **Edit Routes:**
   - Add Route: Destination `0.0.0.0/0`, Target to `NAT Gateway AZ1`.
   - Save Changes.

3. **Subnet Associations:**
   - Edit Subnet Associations.
   - Select `private-app-subnet-AZ-1` for private app resources.
   - Save Associations.

4. **Create Private Route Table for AZ2:**
   - Click on **Create Route Table**.
   - **Name:** `edmunds-private-rt-az2`.
   - Select appropriate VPC.

5. **Edit Routes:**
   - Add Route: Destination `0.0.0.0/0`, Target to `NAT Gateway AZ2`.
   - Save Changes.

6. **Subnet Associations:**
   - Edit Subnet Associations.
   - Select `private-app-subnet-AZ-2` for private app resources.
   - Save Associations.

### Step 5: Create Security Groups

#### Instructions:

#### Internet-Facing Security Group

1. **Navigate to Security Groups in AWS Console:**
   - Select the appropriate VPC.
   - Click **Create Security Group**.

2. **Name:** `internet-facing-sg`
   - **Description:** Security group for internet-facing load balancer.
   - **Inbound Rules:**
     - Allow HTTP (Port 80) from specific IP (e.g., your public IP).

3. Click **Create Security Group**.

#### Web Tier Security Group

1. **Navigate to Security Groups in AWS Console:**
   - Select the appropriate VPC.
   - Click **Create Security Group**.

2. **Name:** `webtier-sg`
   - **Description:** Security group for web tier instances.
   - **Inbound Rules:**
     - Allow HTTP (Port 80) from specific IP (e.g., your public IP).
     - Allow HTTP (Port 80) from `internet-facing-sg` (internet-facing load balancer).

3. Click **Create Security Group**.

#### Internal Load Balancer Security Group

1. **Navigate to Security Groups in AWS Console:**
   - Select the appropriate VPC.
   - Click **Create Security Group**.

2. **Name:** `internal-lb-sg`
   - **Description:** Security group for internal load balancer.
   - **Inbound Rules:**
     - Allow HTTP (Port 80) from `webtier-sg` (web tier instances).

3. Click **Create Security Group**.

#### App Tier Security Group

1. **Navigate to Security Groups in AWS Console:**
   - Select the appropriate VPC.
   - Click **Create Security Group**.

2. **Name:** `apptier-sg`
   - **Description:** Security group for application tier instances.
   - **Inbound Rules:**
     - Allow custom TCP (Port 4000) from specific IP (e.g., your public IP).
     - Allow custom TCP (Port 4000) from `internal-lb-sg` (internal load balancer).

3. Click **Create Security Group**.

#### Database Tier Security Group

1. **Navigate to Security Groups in AWS Console:**
   - Select the appropriate VPC.
   - Click **Create Security Group**.

2. **Name:** `dbtier-sg`
   - **Description:** Security group for database tier instances.
   - **Inbound Rules:**
     - Allow MySQL/Aurora (Port 3306) from `apptier-sg` (application tier instances).

3. Click **Create Security Group**.

## Conclusion

By following these steps, you have successfully set up the foundational networking and security components for an AWS Three-Tier Architecture. Ensure to test each component and configuration to verify functionality.
