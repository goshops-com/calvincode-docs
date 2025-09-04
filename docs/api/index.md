# API Reference

Calvin Code provides programmatic access to AI-powered e-commerce generation through our REST API. While our conversational interface is the primary way to create applications, the API enables integration with existing tools and workflows.

!!! info "API Access"
    The Calvin Code API is currently in beta. Contact us for access and documentation.

## Overview

The Calvin Code API allows you to:

- **Create Projects**: Initialize new e-commerce applications programmatically
- **Manage Conversations**: Send requirements and receive generated code
- **Deploy Applications**: Trigger deployment to various platforms
- **Monitor Status**: Check build and deployment progress
- **Access Analytics**: Retrieve performance and usage metrics

## Authentication

All API requests require authentication using API keys:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     https://api.calvin.gopersonal.com/v1/projects
```

## Base URL

```
https://api.calvin.gopersonal.com/v1
```

## Core Endpoints

### Projects

Create and manage e-commerce projects:

```http
GET    /projects           # List all projects
POST   /projects           # Create new project
GET    /projects/{id}      # Get project details
PUT    /projects/{id}      # Update project
DELETE /projects/{id}      # Delete project
```

### Conversations

Manage AI conversations for project development:

```http
POST   /projects/{id}/conversations    # Start new conversation
GET    /projects/{id}/conversations    # List conversations
POST   /projects/{id}/messages         # Send message to AI
GET    /projects/{id}/messages         # Get conversation history
```

### Deployments

Handle application deployment:

```http
POST   /projects/{id}/deploy      # Deploy project
GET    /projects/{id}/deployments # List deployments
GET    /deployments/{id}          # Get deployment status
POST   /deployments/{id}/rollback # Rollback deployment
```

## Example Requests

### Create a New Project

```json
POST /v1/projects
{
  "name": "My Jewelry Store",
  "description": "Handmade jewelry e-commerce platform",
  "type": "ecommerce",
  "configuration": {
    "theme": "modern",
    "features": ["payments", "inventory", "reviews"],
    "integrations": ["stripe", "bigquery"]
  }
}
```

### Send a Message to AI

```json
POST /v1/projects/proj_123/messages
{
  "message": "Add a wishlist feature and customer reviews section",
  "context": {
    "current_features": ["shopping_cart", "payments"],
    "user_preferences": {
      "style": "minimalist",
      "colors": ["purple", "white"]
    }
  }
}
```

### Deploy Project

```json
POST /v1/projects/proj_123/deploy
{
  "environment": "production",
  "domain": "my-jewelry-store.com",
  "ssl": true,
  "cdn": true
}
```

## Response Format

All API responses follow a consistent format:

```json
{
  "success": true,
  "data": {
    // Response data here
  },
  "meta": {
    "timestamp": "2024-01-15T10:30:00Z",
    "request_id": "req_abc123",
    "version": "v1"
  }
}
```

### Error Responses

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid project configuration",
    "details": {
      "field": "theme",
      "issue": "Theme 'invalid-theme' does not exist"
    }
  },
  "meta": {
    "timestamp": "2024-01-15T10:30:00Z",
    "request_id": "req_abc123"
  }
}
```

## Webhooks

Calvin Code can send webhook notifications for key events:

### Supported Events

- `project.created` - New project created
- `conversation.updated` - AI generated new code
- `deployment.started` - Deployment initiated
- `deployment.completed` - Deployment finished
- `deployment.failed` - Deployment encountered errors

### Webhook Payload Example

```json
{
  "event": "deployment.completed",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "project_id": "proj_123",
    "deployment_id": "dep_456",
    "environment": "production",
    "url": "https://my-store.calvin.gopersonal.com",
    "status": "success"
  }
}
```

### Setting Up Webhooks

```json
POST /v1/webhooks
{
  "url": "https://your-app.com/webhooks/calvin",
  "events": ["deployment.completed", "project.created"],
  "secret": "your-webhook-secret"
}
```

## Rate Limits

| Endpoint Type | Rate Limit |
|--------------|------------|
| Project Management | 100 requests/hour |
| Conversations | 50 messages/hour |
| Deployments | 10 requests/hour |
| Webhooks | 1000 requests/hour |

## SDKs and Libraries

### JavaScript/Node.js

```bash
npm install @calvin-code/api-client
```

```javascript
import CalvinCode from '@calvin-code/api-client';

const client = new CalvinCode({
  apiKey: process.env.CALVIN_API_KEY
});

const project = await client.projects.create({
  name: 'My Store',
  type: 'ecommerce'
});
```

### Python

```bash
pip install calvin-code-api
```

```python
from calvin_code import CalvinCodeClient

client = CalvinCodeClient(api_key='your-api-key')

project = client.projects.create(
    name='My Store',
    type='ecommerce'
)
```

## Integration Examples

### CI/CD Pipeline Integration

```yaml
# GitHub Actions example
name: Deploy with Calvin Code
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Deploy to Calvin Code
        run: |
          curl -X POST \
            -H "Authorization: Bearer ${{ secrets.CALVIN_API_KEY }}" \
            -H "Content-Type: application/json" \
            -d '{"environment": "production"}' \
            https://api.calvin.gopersonal.com/v1/projects/${{ secrets.PROJECT_ID }}/deploy
```

### Monitoring Integration

```javascript
// Monitor deployment status
const checkDeployment = async (deploymentId) => {
  const response = await fetch(`/v1/deployments/${deploymentId}`, {
    headers: {
      'Authorization': `Bearer ${apiKey}`
    }
  });
  
  const deployment = await response.json();
  
  if (deployment.data.status === 'completed') {
    console.log(`Deployment successful: ${deployment.data.url}`);
  } else if (deployment.data.status === 'failed') {
    console.error(`Deployment failed: ${deployment.data.error}`);
  }
};
```

## Getting Started

1. **Request API Access**: [Contact us](https://www.gopersonal.com/es/contact) for beta access
2. **Get API Keys**: Retrieve your authentication credentials
3. **Test Integration**: Start with our sandbox environment
4. **Build Integration**: Use our SDKs or direct HTTP calls
5. **Go Live**: Deploy to production with monitoring

---

For more information, examples, and support, [schedule a technical consultation](https://www.gopersonal.com/es/contact) with our API team.