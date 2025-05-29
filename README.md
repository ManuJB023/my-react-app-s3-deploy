# Deploy React App to AWS S3 + CloudFront (via GitHub Actions)

This project demonstrates how to deploy a React application to AWS S3 and CloudFront using GitHub Actions for CI/CD.

## 🚀 Features
- Automated deployment to AWS S3 via GitHub Actions
- CloudFront CDN for global content delivery
- Cache invalidation on every deployment
- Secure S3 bucket access via Origin Access Control (OAC)

## 🔧 Prerequisites
- AWS account with permissions to:
  - Create S3 buckets
  - Configure CloudFront distributions
  - Create IAM users with programmatic access
- GitHub repository for your React app
- Node.js 18+ installed locally

- The workflow does the following:

1. **Triggers** on every `push` to the `main` branch.
2. **Installs dependencies** and builds the React app.
3. **Deploys** the `/build` folder to an AWS S3 bucket.
4. **Invalidates CloudFront cache** to reflect updates immediately.

---

## 🌐 Live Deployment

📍 **URL**: [https://d8z73yzuf2mkb.cloudfront.net](https://d8z73yzuf2mkb.cloudfront.net)

## ⚙️ AWS Setup

### 1. Create S3 Bucket
- Name: `my-react-app-bucket-devops`
- Disable *Block all public access* (we'll restrict access via CloudFront OAC)
- Enable static website hosting (optional, only for testing)

### 2. Set Up CloudFront
- Create distribution with S3 as origin
- Enable **Origin Access Control (OAC)**
- Set default root object to `index.html`
- Configure custom error responses for SPA routing:
  - 403 → `/index.html` (200)
  - 404 → `/index.html` (200)

## 🔐 AWS Configuration

Set these GitHub secrets in your repo:

| Secret Name                  | Description                                |
|-----------------------------|--------------------------------------------|
| `AWS_ACCESS_KEY_ID`         | Your AWS access key                        |
| `AWS_SECRET_ACCESS_KEY`     | Your AWS secret access key                |
| `AWS_REGION`                | Your AWS region (e.g., `us-east-1`)       |
| `AWS_S3_BUCKET`             | Your S3 bucket name                        |
| `CLOUDFRONT_DISTRIBUTION_ID`| Your CloudFront distribution ID            |

---

## 🧠 Notes

- **S3 Bucket** should be configured for static website hosting, OR allow CloudFront access via Origin Access Control (OAC).
- Ensure `index.html` is set as the **default root object** in CloudFront.
- A proper **bucket policy** must be applied for CloudFront access (if using OAC).

### 3. IAM User for GitHub Actions
Create a user with:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:ListBucket",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-react-app-bucket-devops",
                "arn:aws:s3:::my-react-app-bucket-devops/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "cloudfront:CreateInvalidation",
            "Resource": "arn:aws:cloudfront::YOUR_ACCOUNT_ID:distribution/YOUR_DISTRIBUTION_ID"
        }
    ]
}

