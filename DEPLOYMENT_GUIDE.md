# Deployment Guide for Polo GGB Research Assistant

## Overview
This guide explains how to securely deploy your Research Assistant application without exposing credentials in your GitHub repository.

## The Problem You Had
- Your application was trying to create temporary JSON credential files
- These files couldn't be committed to GitHub for security reasons
- The deployed application couldn't access credentials, causing authentication errors

## The Solution
We've updated the application to use environment variables instead of JSON files, which is more secure and deployment-friendly.

## Setting Up Credentials for Deployment

### For Streamlit Cloud Deployment

1. **Go to your Streamlit Cloud dashboard**
2. **Navigate to your app's settings**
3. **Add the following secrets in the "Secrets" section:**

```toml
[gcp_service_account]
type = "service_account"
project_id = "your-project-id"
private_key_id = "your-private-key-id"
private_key = "-----BEGIN PRIVATE KEY-----\nYOUR_PRIVATE_KEY_HERE\n-----END PRIVATE KEY-----\n"
client_email = "your-service-account@your-project.iam.gserviceaccount.com"
client_id = "your-client-id"
auth_uri = "https://accounts.google.com/o/oauth2/auth"
token_uri = "https://oauth2.googleapis.com/token"
auth_provider_x509_cert_url = "https://www.googleapis.com/oauth2/v1/certs"
client_x509_cert_url = "https://www.googleapis.com/robot/v1/metadata/x509/your-service-account%40your-project.iam.gserviceaccount.com"

[vertex_ai]
VERTEXAI_PROJECT = "your-project-id"
VERTEXAI_LOCATION = "us-central1"

[elasticsearch]
cloud_id = "your-elastic-cloud-id"
username = "your-elastic-username"
password = "your-elastic-password"

[app_config]
gcs_bucket_name = "your-gcs-bucket-name"
```

### For Other Deployment Platforms

#### Heroku
Add environment variables in your Heroku dashboard:
```
GOOGLE_APPLICATION_CREDENTIALS={"type":"service_account","project_id":"your-project-id",...}
VERTEXAI_PROJECT=your-project-id
VERTEXAI_LOCATION=us-central1
ELASTIC_CLOUD_ID=your-elastic-cloud-id
ELASTIC_USERNAME=your-elastic-username
ELASTIC_PASSWORD=your-elastic-password
GCS_BUCKET_NAME=your-gcs-bucket-name
```

#### Railway
Add environment variables in your Railway project settings:
```
GOOGLE_APPLICATION_CREDENTIALS={"type":"service_account","project_id":"your-project-id",...}
VERTEXAI_PROJECT=your-project-id
VERTEXAI_LOCATION=us-central1
ELASTIC_CLOUD_ID=your-elastic-cloud-id
ELASTIC_USERNAME=your-elastic-username
ELASTIC_PASSWORD=your-elastic-password
GCS_BUCKET_NAME=your-gcs-bucket-name
```

#### Docker
Create a `.env` file (never commit this to git):
```env
GOOGLE_APPLICATION_CREDENTIALS={"type":"service_account","project_id":"your-project-id",...}
VERTEXAI_PROJECT=your-project-id
VERTEXAI_LOCATION=us-central1
ELASTIC_CLOUD_ID=your-elastic-cloud-id
ELASTIC_USERNAME=your-elastic-username
ELASTIC_PASSWORD=your-elastic-password
GCS_BUCKET_NAME=your-gcs-bucket-name
```

## Getting Your Google Cloud Service Account Credentials

1. **Go to Google Cloud Console**
2. **Navigate to IAM & Admin > Service Accounts**
3. **Create a new service account or use existing one**
4. **Generate a new JSON key:**
   - Click on your service account
   - Go to "Keys" tab
   - Click "Add Key" > "Create new key"
   - Choose "JSON" format
   - Download the file

5. **Copy the contents of the JSON file** and paste it into your deployment platform's secrets/environment variables

## Security Best Practices

### ✅ DO:
- Use environment variables for credentials
- Keep credentials out of your code repository
- Use service accounts with minimal required permissions
- Regularly rotate your credentials
- Monitor access logs

### ❌ DON'T:
- Commit credential files to git
- Hardcode credentials in your code
- Use overly broad permissions
- Share credentials in plain text

## Testing Your Deployment

After setting up credentials:

1. **Deploy your application**
2. **Check the logs** for any authentication errors
3. **Test the login functionality**
4. **Verify cloud storage access**

## Troubleshooting

### Common Issues:

1. **"Invalid JWT Signature" Error**
   - Check that your service account JSON is correctly formatted
   - Ensure the private key is properly escaped (newlines as `\n`)

2. **"Bucket not accessible" Error**
   - Verify your service account has Storage Admin or Storage Object Admin permissions
   - Check that the bucket name is correct

3. **"Missing secret configuration" Error**
   - Ensure all required secrets are set in your deployment platform
   - Check the secret names match exactly (case-sensitive)

### Getting Help:
- Check your deployment platform's logs
- Verify all environment variables are set correctly
- Test credentials locally first using a `.streamlit/secrets.toml` file

## Local Development

For local development, create a `.streamlit/secrets.toml` file (this is already in your .gitignore):

```toml
[gcp_service_account]
type = "service_account"
project_id = "your-project-id"
private_key_id = "your-private-key-id"
private_key = "-----BEGIN PRIVATE KEY-----\nYOUR_PRIVATE_KEY_HERE\n-----END PRIVATE KEY-----\n"
client_email = "your-service-account@your-project.iam.gserviceaccount.com"
client_id = "your-client-id"
auth_uri = "https://accounts.google.com/o/oauth2/auth"
token_uri = "https://oauth2.googleapis.com/token"
auth_provider_x509_cert_url = "https://www.googleapis.com/oauth2/v1/certs"
client_x509_cert_url = "https://www.googleapis.com/robot/v1/metadata/x509/your-service-account%40your-project.iam.gserviceaccount.com"

[vertex_ai]
VERTEXAI_PROJECT = "your-project-id"
VERTEXAI_LOCATION = "us-central1"

[elasticsearch]
cloud_id = "your-elastic-cloud-id"
username = "your-elastic-username"
password = "your-elastic-password"

[app_config]
gcs_bucket_name = "your-gcs-bucket-name"
```

## Summary

The key changes made to fix your authentication issues:

1. **Removed temporary JSON file creation** - No more `gcp_credentials.json` files
2. **Updated to use environment variables** - More secure and deployment-friendly
3. **Enhanced .gitignore** - Prevents accidental credential commits
4. **Simplified authentication flow** - No more file waiting loops

Your application should now work properly in deployed environments without exposing credentials in your GitHub repository.
