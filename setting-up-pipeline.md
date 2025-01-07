# Automatic Pipeline for Deploying a Static Website to S3 with AWS

> **Note**: This is just a guide, and in practice, options may vary within the AWS console. However, it serves as guidance to configure the pipeline efficiently.

---

## Prerequisites

- **GitHub Repository** already created: `aws-s3-static-website-pipeline` with your static website.
- **AWS Account** with permissions to create and use services like CodePipeline, CodeBuild, and S3.

---

## Step 1: Create the S3 Bucket for Website Hosting

### 1.1. Access the AWS Console

- Go to the **AWS Management Console** and log in.

### 1.2. Create the S3 Bucket

- In the console, search for **S3** and enter the service.
- Click on **Create bucket** and configure:
  - **Bucket Name**: `my-static-bucket` (Choose a unique name).
  - **Region**: Select the region closest to you.
- Disable **Block Public Access** to allow public access to the site.
- Click on **Create**.

### 1.3. Configure the Bucket to Host a Static Website

- Inside the bucket, go to the **Properties** tab.
- Under **Static website hosting**, click on **Edit**.
- Enable the option and configure:
  - **Index document**: `index.html`.
  - **Error document**: (leave empty or set to `404.html`).
- Click on **Save changes**.

---

## Step 2: Create a CodeBuild Project to Validate the Website

### 2.1. Access the CodeBuild Console

- In the AWS Management Console, search for **CodeBuild**.

### 2.2. Create a New Build Project

- Click on **Create build project**.
- Fill in the fields:
  - **Project Name**: `ValidateStaticWebsite`.
  - **Source Provider**: GitHub.
  - Connect your GitHub account (or it will already be connected if AWS and GitHub are linked).
  - **Repository**: Select the `aws-s3-static-website-pipeline` repository.
  - **Branch**: Choose the main branch (usually `main`).

### 2.3. Environment Configuration

Under **Environment**, configure:

- **Build Environment**: Ubuntu Standard (CodeBuild Image).
- **Compute Type**: Default.

### 2.4. Buildspec

Enter the following content to validate the presence of `index.html`:

```yaml
version: 0.2
phases:
  build:
    commands:
      - echo "Validating website structure..."
      - test -f index.html && echo "index.html found."
artifacts:
  files:
    - '**/*'
```

## Step 3: Create the Pipeline in CodePipeline

### 3.1. Access the CodePipeline Console

- In the AWS Management Console, search for **CodePipeline** and access the service.

### 3.2. Create a New Pipeline

- Click on **Create pipeline**.
- Configure the pipeline:
  - **Pipeline Name**: `StaticWebsitePipeline`.
  - **Service Role**: Select **New service role** to create a new role automatically.
- Click on **Next**.

---

## Step 4: Configure the Source Stage in CodePipeline

### 4.1. Source Configuration

- **Source Provider**: GitHub.
- **Repository**: Choose the `aws-s3-static-website-pipeline` repository.
- **Branch**: Select the main branch (usually `main`).
- **Change detection options**: Select **GitHub Webhook**.

Click on **Next**.

---

## Step 5: Configure the Build Stage in CodePipeline

- **Build Provider**: Choose **AWS CodeBuild**.
- **Project Name**: Select the `ValidateStaticWebsite` project created in Step 2.
- Click on **Next**.

---

## Step 6: Configure the Deploy Stage to S3

- **Deploy Provider**: Choose **Amazon S3**.
- **Bucket**: Select the `my-static-bucket` bucket (created in Step 1).
- **Extract Artifacts**: Check the option **Extract artifacts**.
- Click on **Next**.

---

## Step 7: Finalize Pipeline Creation

### 7.1. Review

- Review the configurations: **Source** (GitHub), **Build** (CodeBuild), and **Deploy** (S3).
- Click on **Create pipeline**.

### 7.2. Pipeline in Action

- The pipeline will be created and triggered automatically whenever a new commit is made to the GitHub repository.

---

## Step 8: Test the Automatic Deploy

### 8.1. Make a Test Commit

Run the following commands in your terminal to make a test commit:

```bash
git add .
git commit -m "Site update"
git push origin main
```

### 8.2. Check the Pipeline

- CodePipeline will be triggered automatically and will run the process. You can monitor the progress by going to the **CodePipeline** console.
- The stages will display as either **In progress** or **Succeeded** depending on the pipeline’s execution.

### 8.3. Check the Website

- Once the pipeline completes successfully, you can access the website by visiting the **S3 bucket URL**. If you have configured CloudFront, you can use the CloudFront URL instead.
- The S3 URL will typically be in the following format:
  
  ```text
  http://my-static-bucket.s3-website-us-east-1.amazonaws.com
  ```

## Troubleshooting: Access Denied

This error usually occurs when the S3 bucket is configured not to allow public access. The access policy needs to be adjusted to allow the files to be accessed via a public URL.

### Step 1: Adjust Public Access Settings in the S3 Bucket

1. Access the **S3 Bucket**:
   - In the AWS Management Console, go to the S3 service.
   - Select the bucket you created (e.g., `my-static-bucket`).

2. **Allow Public Access**:
   - In the **Permissions** tab, click on **Bucket Policy**.
   - Add the following policy to ensure the site files are public:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-static-bucket/*"
    }
  ]
}
```

### Step 2: Verify Public Access Block Settings

1. **Adjust Block Settings**:
   - Still in the **Permissions** tab of the bucket, click on **Block public access** (bucket settings).
   - Select **Edit** and uncheck the options that block public access (if necessary).
   - Uncheck **Block all public access**, if enabled.

2. **Save Changes**.

### Step 3: Test Site Access

After saving these settings, access the **public URL** of your bucket to check if the site is now accessible. The link will look something like this:

```text
http://my-static-bucket.s3-website-us-east-1.amazonaws.com
```
