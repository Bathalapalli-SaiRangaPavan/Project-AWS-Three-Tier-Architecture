
## Step 1 - Download Code from GitHub Repository

To begin, clone or download the repository containing the necessary code for the web and app tiers. Here's the GitHub repository link: [AWS Three-Tier Web Architecture Workshop](https://github.com/aws-samples/aws-three-tier-web-architecture-workshop).

**Note:** This code will be used at a later point in time. You can find the application code [here](https://github.com/aws-samples/aws-three-tier-web-architecture-workshop/tree/main/application-code).

### Learning Objectives in Application Code:

- **App Tier:**
  The application tier consists of a Node.js application. This part of the code handles the business logic and data processing for your application.

- **Web Tier:**
  The web tier utilizes Nginx as a web server with specific configuration details. It serves the frontend of your application, manages API calls, and interacts with the application tier.

Understanding these tiers and their configurations is crucial for deploying and managing your three-tier architecture on AWS effectively.

---

## Step 2 - S3 Bucket Creation

1. **Search for S3 in the AWS Management Console:**
   - Navigate to the AWS Management Console.
   - In the search bar, type "S3" and select it from the dropdown.

2. **Click on "Create bucket":**
   - Once in the S3 dashboard, click on the "Create bucket" button.

3. **Configure bucket settings:**
   - **Name and region:**
     - Choose a unique name for your bucket, e.g., `edmunds-project`.
     - Select the region as `us-east-1` (N. Virginia), or choose your preferred region.

4. **Review and create:**
   - Review your bucket configuration details.
   - Click "Create bucket" to complete the setup.

---

## Step 3 - IAM Role Creation

1. **Navigate to the IAM (Identity and Access Management) Console:**
   - In the AWS Management Console, navigate to IAM by searching for it in the services.

2. **Click on "Roles":**
   - In the left-hand menu of IAM, click on "Roles".

3. **Create a Role:**
   - Click on the "Create role" button to start creating a new IAM role.

4. **Choose the type of trusted entity:**
   - Select "AWS service" as the type of trusted entity.

5. **Select a use case:**
   - Under "Use case", choose "EC2" as the service that will use this role.

6. **Attach permissions policies:**
   - Search for and select the following permissions policies to attach:
     - "AmazonS3ReadOnlyAccess": Grants read-only access to S3 buckets.
     - "AmazonSSMManagedInstanceCore": Allows EC2 instances to use Systems Manager (SSM) to manage and configure them.

7. **Review and name the role:**
   - Give your role a name, for example, `S3-SSM-access-role`.
   - Optionally, add a description for the role.

8. **Create the role:**
   - Click "Create role" to finish creating the IAM role.
