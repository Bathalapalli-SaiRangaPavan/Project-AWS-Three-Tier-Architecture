## Database Deployment: AWS RDS

### Step 1: Create Subnet Groups

As we know, when creating a database, we should create subnet groups. Subnet groups help us to create Multi-AZ databases.

1. **Navigate to RDS:**
   - Go to the RDS console on AWS.

2. **Create a Subnet Group:**
   - Click on **Subnet Groups** from the navigation pane.
   - Click on **Create DB Subnet Group**.

3. **Configure Subnet Group:**
   - **Name:** Enter `Edmunds-RDS-SG`.
   - **Description:** Enter `Edmunds-RDS-SG`.
   - **VPC:** Select your VPC.
   - **Add Subnets:**
     - **Availability Zones:** Select `us-east-1a` and `us-east-1b`.
     - **Subnets:**
       - Select `10.0.2.0/24` in `us-east-1a`.
       - Select `10.0.5.0/24` in `us-east-1b`.

4. **Create the Subnet Group:**
   - Click on **Create** to finalize the subnet group setup.




### Step 2: Create an Amazon Aurora Database

In the previous step, we created a subnet group to use while creating our database. Now, we will create an Amazon Aurora database configured for a development and test environment.

1. **Navigate to RDS:**
   - Go to the RDS console on AWS.

2. **Create a Database:**
   - Click on **Create Database**.

3. **Database Configuration:**
   - **Engine:** Select **Amazon Aurora**.
   - **Edition:** Choose **Aurora MySQL-Compatible Edition** or **Aurora PostgreSQL-Compatible Edition**.
   - **Template:** Select **Dev/Test**.
   - **DB Cluster Identifier:** Provide a unique identifier for the database cluster.
   - **Master Username:** Enter `admin`.
   - **Master Password:** Enter `<give your password>`.

4. **DB Instance Class:**
   - Select **db.r5.large** for testing purposes.

5. **Multi-AZ Deployment:**
   - Enable Multi-AZ deployment for high availability.

6. **Network & Security:**
   - **DB Subnet Group:** Select the subnet group created earlier (`Edmunds-RDS-SG`).
   - **VPC Security Group:** Select the security group created for the database (`DB tierSG') # we created for db
   - **Publicly Accessible:** Choose **No**.

7. **Additional Configuration:**
   - Leave other settings as default unless specific configurations are needed.

8. **Create the Database:**
   - Click on **Create Database** to finalize the setup.

The database creation process will take some time to complete.


### Step 3 - App tier instance Deployment: EC2

#### Create the App Tier Instance

To create the App Tier instance, follow these steps:

1. **Navigate to EC2:**
   - Go to the EC2 console on AWS.

2. **Launch Instance:**
   - Click on **Launch Instance**.
   - **Name:** Enter `app-server`.
   - **Amazon Machine Image (AMI):** Choose **Amazon Linux 2**.
   - **Instance Type:** Select **t2.micro**.

3. **Key Pair:**
   - Choose **Proceed without a key pair**.
   - Note: We will use SSM for logging into the instance.

4. **Network Settings:**
   - **VPC:** Choose `edmunds-vpc`.
   - **Subnet:** Choose `Private-app-subnet`.
   - **Auto-assign Public IP:** Select **Disable**.
   - **Security Group:** Select the existing security group (`app-tier-sg`).

5. **Advanced Settings:**
   - **IAM Role:** Select the IAM role created for S3 and SSM access (`s3-ssm-access-role`).

6. **Launch Instance:**
   - Click on **Launch Instance** to finalize the setup.

Next, we will configure the software stack and database schema, and test database connectivity once the database is ready.



### Step 4: Configure the App Tier Instance and Database Connectivity

#### Connect to the Instance:
1. **Connect via SSM:**
   - Navigate to the EC2 console.
   - Select the running instance.
   - Click on **Connect** and choose **Session Manager**.
   - Click **Connect** to open the CLI in the browser.

2. **Verify Internet Access:**
   - Run the command: `ping 8.8.8.8`
   - This confirms internet access through the NAT gateway.

#### Install MySQL Client:
1. **Switch to the EC2 User:**
   - Run: `sudo su - ec2-user`

2. **Install MySQL:**
   - Run: `sudo yum install -y mysql`

#### Connect to the RDS Database:
1. **Obtain RDS Endpoint:**
   - Go to the RDS console.
   - Select your database instance and copy the endpoint.

2. **Connect to the Database:**
   - Run: `mysql -h <RDS-endpoint> -u admin -p`
   - Enter the password: `<give your password>`

#### Configure Database Schema:
1. **Create Database:**
   - Run: `CREATE DATABASE webapp_db;`

2. **Show Databases:**
   - Run: `SHOW DATABASES;`
   - Confirm `webapp_db` is listed.

3. **Use the Database:**
   - Run: `USE webapp_db;`

4. **Create a Table:**
   - Run:
     ```sql
     CREATE TABLE IF NOT EXISTS transactions (
       id INT AUTO_INCREMENT PRIMARY KEY,
       amount DECIMAL(10,2) NOT NULL,
       description VARCHAR(255) NOT NULL
     );
     ```

5. **Show Tables:**
   - Run: `SHOW TABLES;`
   - Confirm `transactions` table is listed.

6. **Insert Data:**
   - Run:
     ```sql
     INSERT INTO transactions (amount, description) VALUES (100.00, 'Initial Deposit');
     INSERT INTO transactions (amount, description) VALUES (300.00, 'Food');
     ```

7. **Query Data:**
   - Run: `SELECT * FROM transactions;`
   - Confirm the inserted rows are displayed.

8. **Exit MySQL:**
   - Run: `exit`

The app and database communication is properly set up. Next, we will configure the application instance.

