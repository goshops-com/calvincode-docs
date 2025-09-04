# Google Cloud Service Account Setup for BigQuery MCP

This comprehensive guide shows you how to create a Google Cloud service account with the necessary permissions for BigQuery MCP integration, using both the Google Cloud Console UI and command-line tools.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Method 1: Google Cloud Console (UI)](#method-1-google-cloud-console-ui)
3. [Method 2: Command Line (gcloud CLI)](#method-2-command-line-gcloud-cli)
4. [Required Permissions](#required-permissions)
5. [Converting to Base64](#converting-to-base64)
6. [Troubleshooting](#troubleshooting)

## Prerequisites

- A Google Cloud Platform account
- An existing GCP project (or create a new one)
- Billing enabled on your project
- Project Owner or Editor permissions, or specific IAM roles:
  - `roles/iam.serviceAccountAdmin`
  - `roles/iam.serviceAccountKeyAdmin`
  - `roles/resourcemanager.projectIamAdmin`

## Method 1: Google Cloud Console (UI)

### Step 1: Access the Google Cloud Console

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Sign in with your Google account
3. Select your project from the dropdown at the top of the page

### Step 2: Enable Required APIs

1. Navigate to **APIs & Services** → **Library**
2. Search for "BigQuery API" and click on it
3. Click **Enable** if not already enabled
4. Repeat for "Cloud Resource Manager API" if creating a new project

### Step 3: Create a Service Account

1. Navigate to **IAM & Admin** → **Service Accounts**
2. Click **+ CREATE SERVICE ACCOUNT**
3. Fill in the service account details:
   - **Service account name**: `bigquery-mcp-service`
   - **Service account ID**: `bigquery-mcp-service` (auto-generated)
   - **Description**: `Service account for BigQuery MCP access`
4. Click **CREATE AND CONTINUE**

### Step 4: Grant Permissions

1. In the **Grant this service account access to project** section:
   - Click **Select a role**
   - Search for "BigQuery Admin"
   - Select **BigQuery Admin** (`roles/bigquery.admin`)
2. Click **CONTINUE**
3. Skip the optional "Grant users access to this service account" section
4. Click **DONE**

### Step 5: Create and Download Service Account Key

1. In the Service Accounts list, find your newly created service account
2. Click on the service account name to open its details
3. Navigate to the **KEYS** tab
4. Click **ADD KEY** → **Create new key**
5. Select **JSON** as the key type
6. Click **CREATE**
7. The JSON key file will automatically download to your computer
8. **Important**: Store this file securely and never share it publicly

### Step 6: Note Your Project ID

1. In the Google Cloud Console header, note your **Project ID**
2. You'll need this for the MCP configuration

## Method 2: Command Line (gcloud CLI)

### Step 1: Install and Setup gcloud CLI

1. Install the Google Cloud CLI from [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)
2. Authenticate with your Google account:
   ```bash
   gcloud auth login
   ```
3. Set your project (replace `YOUR_PROJECT_ID`):
   ```bash
   gcloud config set project YOUR_PROJECT_ID
   ```

### Step 2: Enable Required APIs

```bash
# Enable BigQuery API
gcloud services enable bigquery.googleapis.com

# Enable Cloud Resource Manager API (if needed)
gcloud services enable cloudresourcemanager.googleapis.com
```

### Step 3: Create Service Account

```bash
gcloud iam service-accounts create bigquery-mcp-service \
    --display-name="BigQuery MCP Service Account" \
    --description="Service account for BigQuery MCP access"
```

### Step 4: Grant BigQuery Permissions

```bash
# Get your project ID
PROJECT_ID=$(gcloud config get-value project)

# Grant BigQuery Admin role to the service account
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member="serviceAccount:bigquery-mcp-service@$PROJECT_ID.iam.gserviceaccount.com" \
    --role="roles/bigquery.admin"
```

### Step 5: Create and Download Service Account Key

```bash
# Create and download the service account key
gcloud iam service-accounts keys create ./bigquery-service-account.json \
    --iam-account=bigquery-mcp-service@$PROJECT_ID.iam.gserviceaccount.com

# Verify the file was created
ls -la bigquery-service-account.json
```

## Required Permissions

The service account needs the following minimum permissions for BigQuery MCP:

### Essential Role
- **BigQuery Admin** (`roles/bigquery.admin`) - Full access to BigQuery resources

### Alternative Granular Permissions (if you prefer minimal permissions)
Instead of BigQuery Admin, you can grant these specific permissions:
- `bigquery.datasets.create`
- `bigquery.datasets.get`
- `bigquery.datasets.getIamPolicy`
- `bigquery.datasets.update`
- `bigquery.jobs.create`
- `bigquery.jobs.get`
- `bigquery.jobs.list`
- `bigquery.tables.create`
- `bigquery.tables.delete`
- `bigquery.tables.get`
- `bigquery.tables.getData`
- `bigquery.tables.list`
- `bigquery.tables.update`
- `bigquery.tables.updateData`

### To grant granular permissions via CLI:
```bash
# Create a custom role with minimal permissions
gcloud iam roles create bigqueryMcpRole \
    --project=$PROJECT_ID \
    --title="BigQuery MCP Role" \
    --description="Minimal permissions for BigQuery MCP" \
    --permissions="bigquery.datasets.create,bigquery.datasets.get,bigquery.datasets.getIamPolicy,bigquery.datasets.update,bigquery.jobs.create,bigquery.jobs.get,bigquery.jobs.list,bigquery.tables.create,bigquery.tables.delete,bigquery.tables.get,bigquery.tables.getData,bigquery.tables.list,bigquery.tables.update,bigquery.tables.updateData"

# Assign the custom role
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member="serviceAccount:bigquery-mcp-service@$PROJECT_ID.iam.gserviceaccount.com" \
    --role="projects/$PROJECT_ID/roles/bigqueryMcpRole"
```

## Converting to Base64

For some configurations, you may need to convert your service account JSON to base64 encoding.

### Method 1: Online Tool

1. Go to [https://www.base64encode.org/](https://www.base64encode.org/)
2. Copy the entire contents of your `bigquery-service-account.json` file
3. Paste it into the text area on the website
4. Click **Encode** to get the base64 string
5. Copy the resulting base64 string

### Method 2: Command Line (macOS/Linux)

```bash
# Convert JSON file to base64
base64 -i bigquery-service-account.json

# Or save to a file
base64 -i bigquery-service-account.json > service-account-base64.txt

# To decode back (for verification)
base64 -d service-account-base64.txt > decoded-service-account.json
```

### Method 3: Command Line (Windows)

```powershell
# PowerShell method
[Convert]::ToBase64String([IO.File]::ReadAllBytes("bigquery-service-account.json"))

# Or using certutil
certutil -encode bigquery-service-account.json service-account-base64.txt
```

### Method 4: Python (Cross-platform)

```python
import base64
import json

# Read and encode the JSON file
with open('bigquery-service-account.json', 'rb') as f:
    encoded = base64.b64encode(f.read()).decode('utf-8')
    print(encoded)
```

## Next Steps: Configure MCP

Once you have your service account JSON file (and optionally base64 encoded), you can configure the BigQuery MCP:

1. Download the GenAI toolbox:
   ```bash
   curl -O https://storage.googleapis.com/genai-toolbox/v0.13.0/darwin/arm64/toolbox
   chmod +x toolbox
   ```

2. Add the MCP configuration:
   ```bash
   claude mcp add-json bigquery-toolbox '{
     "command": "./toolbox",
     "args": ["--prebuilt", "bigquery", "--stdio"],
     "env": {
       "BIGQUERY_PROJECT": "YOUR_PROJECT_ID",
       "GOOGLE_APPLICATION_CREDENTIALS": "/path/to/bigquery-service-account.json"
     }
   }'
   ```

## Troubleshooting

### Common Issues

**Error: "Permission denied"**
- Ensure your user account has the necessary IAM permissions
- Verify the service account has the correct roles assigned
- Check that the required APIs are enabled

**Error: "Service account not found"**
- Verify the service account was created successfully
- Check that you're using the correct project ID
- Ensure the service account email format is correct

**Error: "Invalid key file"**
- Verify the JSON file is valid and not corrupted
- Check file permissions and ensure it's readable
- Make sure the file path in the configuration is correct

**Error: "Quota exceeded"**
- Check your project's API quotas
- Ensure billing is enabled on your project
- Verify you haven't exceeded service account limits

### Verification Commands

```bash
# List service accounts
gcloud iam service-accounts list

# Check service account permissions
gcloud projects get-iam-policy $PROJECT_ID \
    --flatten="bindings[].members" \
    --format="table(bindings.role)" \
    --filter="bindings.members:bigquery-mcp-service@$PROJECT_ID.iam.gserviceaccount.com"

# Test BigQuery access
bq --project_id=$PROJECT_ID ls

# Validate JSON file
cat bigquery-service-account.json | python -m json.tool
```

## Security Best Practices

1. **Store credentials securely**: Never commit service account keys to version control
2. **Use least privilege**: Grant only the minimum permissions needed
3. **Rotate keys regularly**: Create new keys and delete old ones periodically
4. **Monitor usage**: Enable audit logging to track service account usage
5. **Use short-lived tokens when possible**: Consider using Workload Identity Federation for enhanced security

## Support

If you encounter issues:
1. Check the [Google Cloud Status page](https://status.cloud.google.com/)
2. Review the [BigQuery documentation](https://cloud.google.com/bigquery/docs)
3. Visit the [Google Cloud Console support page](https://console.cloud.google.com/support)
4. Check the Claude MCP documentation for configuration issues
