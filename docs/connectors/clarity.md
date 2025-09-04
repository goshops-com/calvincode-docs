# Microsoft Clarity Data Export API Setup

This comprehensive guide shows you how to set up Microsoft Clarity Data Export API integration with Calvin Code, including how to obtain access tokens and configure the connection.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Obtaining Access Tokens](#obtaining-access-tokens)
3. [Token Configuration](#token-configuration)
4. [API Usage Guidelines](#api-usage-guidelines)
5. [Troubleshooting](#troubleshooting)

## Prerequisites

Before setting up the Clarity Data Export API integration, ensure you have:

- **Active Microsoft Clarity Account**: A registered and active Clarity account
- **Project Admin Access**: You must have project administrator privileges 
- **Clarity Project**: An existing project with data collection enabled
- **Calvin Code Access**: Access to Calvin Code for MCP configuration

## Obtaining Access Tokens

Microsoft Clarity uses JWT tokens for API authentication. Follow these steps to generate your access token:

### Step 1: Access Your Clarity Project Settings

1. Log in to [Microsoft Clarity](https://clarity.microsoft.com/)
2. Navigate to your project dashboard
3. Select the project you want to integrate with
4. Go to **Settings** → **Data Export**

### Step 2: Generate New API Token

1. In the Data Export section, click **"Generate new API token"**
2. Provide a token name that meets the following requirements:

#### Token Name Requirements
- **Length**: 4-32 characters
- **Allowed characters**: 
  - Alphanumeric (A-Z, a-z, 0-9)
  - Hyphens (`-`)
  - Underscores (`_`)
  - Periods (`.`)
- **Not allowed**: Spaces or special characters (`@`, `#`, `$`, etc.)
- **Uniqueness**: Must be unique within the project

#### Example Valid Token Names
```
clarity-calvin-prod
clarity_export_api
data.export.token.2024
calvin-integration-v1
```

### Step 3: Save Your Token Securely

1. After clicking generate, **immediately copy and save the token**
2. Store it in a secure location (password manager, encrypted file, etc.)
3. **Important**: You cannot retrieve the token again after leaving this page

## Token Configuration

### Authentication Method

Microsoft Clarity Data Export API uses Bearer token authentication. Include your token in all API requests:

```http
Authorization: Bearer <Your_API_Token>
```

### Calvin Code MCP Configuration

Configure the Clarity connector in Calvin Code using your generated token:

```bash
# Add Clarity MCP connector
claude mcp add-json clarity-connector '{
  "command": "./toolbox",
  "args": ["--prebuilt", "clarity", "--stdio"],
  "env": {
    "CLARITY_API_TOKEN": "your-generated-token-here",
    "CLARITY_PROJECT_ID": "your-project-id"
  }
}'
```

### Environment Variables

Set up the following environment variables for your integration:

```bash
# Required
CLARITY_API_TOKEN=your-jwt-token-here
CLARITY_PROJECT_ID=your-project-id

# Optional
CLARITY_API_BASE_URL=https://www.clarity.ms/api/v1
```

## API Usage Guidelines

### Rate Limits and Quotas

Microsoft Clarity Data Export API has the following limitations:

- **Daily Request Limit**: Maximum 10 requests per project per day
- **Data Availability**: Data for the past 1-3 days is retrievable
- **Request Frequency**: Plan your data exports carefully due to low daily limit

### Data Export Timeframes

- **Minimum Delay**: 1 day after data collection
- **Maximum History**: 3 days of historical data
- **Update Frequency**: Daily data exports recommended

### Best Practices

1. **Token Security**
   - Store tokens securely using environment variables or secret management
   - Never commit tokens to version control
   - Rotate tokens periodically

2. **Rate Limit Management**
   - Plan your requests carefully (only 10 per day)
   - Implement exponential backoff for failed requests
   - Cache exported data to minimize API calls

3. **Data Compliance**
   - Ensure compliance with data protection regulations (GDPR, CCPA)
   - Implement proper data retention policies
   - Document data usage and processing

4. **Error Handling**
   - Implement robust error handling for rate limit exceeded
   - Handle authentication errors gracefully
   - Log API responses for debugging

### Example API Request

```bash
# Export session data for a specific date
curl -X GET \
  "https://www.clarity.ms/api/v1/projects/{project_id}/exports/sessions/{date}" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

## Troubleshooting

### Common Issues

**Authentication Errors**
- Verify token is correctly formatted and not expired
- Ensure you have project admin privileges
- Check that the project ID is correct

**Rate Limit Exceeded**
- You've reached the 10 requests per day limit
- Wait until the next day to make additional requests
- Plan your data export schedule more efficiently

**Token Generation Issues**
- Ensure token name meets character requirements (4-32 chars, alphanumeric + `-`, `_`, `.`)
- Verify you have project admin access
- Check that token name is unique within the project

**No Data Available**
- Data is only available 1-3 days after collection
- Ensure your project is actively collecting data
- Verify the date range in your API requests

### Access Management

**Important**: Only project administrators can manage access tokens. If you need access:

1. Contact your project administrator
2. Request admin privileges for your account
3. Ensure proper permissions are granted before attempting token generation

### Token Lifecycle

- **Generation**: Tokens are generated immediately upon creation
- **Expiration**: Tokens don't automatically expire but should be rotated regularly
- **Revocation**: Replace tokens if user access changes or for security reasons
- **Management**: Only project admins can create, view, and manage tokens

## Next Steps: Configure Calvin Code

Once you have your Clarity API token:

1. **Test Connection**: Verify token works with a simple API call
2. **Configure MCP**: Add the Clarity connector to Calvin Code
3. **Set Up Data Pipeline**: Define your data export and processing workflow
4. **Monitor Usage**: Track your daily API usage to stay within limits

## Support

If you encounter issues:

1. Check the [Microsoft Clarity Documentation](https://learn.microsoft.com/en-us/clarity/)
2. Verify your project admin permissions
3. Review API rate limits and quotas
4. [Contact support](https://www.gopersonal.com/es/contact) for Calvin Code integration assistance

---

**Security Reminder**: Always keep your API tokens secure and never share them publicly. Treat them like passwords and rotate them regularly for optimal security.