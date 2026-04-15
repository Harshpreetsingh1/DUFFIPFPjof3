# Enterprise React CI/CD Pipeline Documentation

This repository contains a fully automated, production-ready CI/CD system using GitHub Actions.

## Architecture Highlights
- **Parallel CI**: Jobs for linting, typechecking, security, and testing run simultaneously.
- **Environment-Aware CD**: Automatic deployment to Staging (develop branch) and Production (main branch).
- **PR Previews**: Ephemeral deployments for every pull request to verify changes before merging.
- **Rollback System**: One-click manual rollback to any previous version.
- **Notifications**: Slack integration for real-time deployment status.

## Repository Structure
```
.github/workflows/
  ├── ci.yml        # CI Pipeline (parallel jobs)
  ├── cd.yml        # CD Pipeline (staging/prod)
  ├── preview.yml   # PR Preview deployments
  ├── rollback.yml  # Manual rollbacks
  ├── setup.yml     # Reusable setup logic
  └── notify.yml    # Reusable Slack notifications
aws/
  ├── iam-policy.json         # Least-privilege IAM policy
  ├── s3-bucket-policy.json   # S3 Policy template
  └── cloudfront-config.md    # CloudFront Setup Guide
```

## Setup Instructions

### 1. GitHub Secrets
Configure the following secrets in your GitHub repository:
- `AWS_ACCESS_KEY_ID`: IAM user access key.
- `AWS_SECRET_ACCESS_KEY`: IAM user secret key.
- `AWS_REGION`: e.g., `us-east-1`.
- `STAGING_S3_BUCKET`: Name of your staging S3 bucket.
- `PRODUCTION_S3_BUCKET`: Name of your production S3 bucket.
- `PREVIEW_S3_BUCKET`: Name of your preview S3 bucket.
- `STAGING_CLOUDFRONT_ID`: CloudFront distribution ID for staging.
- `PRODUCTION_CLOUDFRONT_ID`: CloudFront distribution ID for production.
- `SLACK_WEBHOOK_URL`: Slack webhook URL for notifications.

### 2. Branch Protection
Recommendation for `main`:
- Require pull request reviews before merging.
- Require status checks to pass (`Continuous Integration`).
- Do not allow force pushes.

Recommendation for `develop`:
- Require status checks to pass (`Continuous Integration`).

## Deployment Flow
1. **Developer Push**: Push to `feature/*`.
2. **Pull Request**: Triggers CI (parallel tests/build) and Preview Deployment.
3. **Merge to `develop`**: Triggers CD to **Staging**.
4. **Merge to `main`**: Triggers CD to **Production**.

## Rollback Procedures
1. Navigate to **Actions** → **Manual Rollback**.
2. Click **Run workflow**.
3. Select the **Environment** (staging/production).
4. Enter the **Version** timestamp or leave as `previous`.
5. Click **Run workflow**.

## Troubleshooting
- **Build Failures**: Check the `build` job logs in CI. Ensure all required `env` variables are defined.
- **S3 Sync Issues**: Verify IAM policy permissions for the specific bucket resource.
- **CloudFront Invalidation Errors**: Ensure the Distribution ID is correct and active.
