# Installation & Setup

Get started with Calvin Code and begin creating AI-powered e-commerce solutions through simple conversation.

## Quick Start

Calvin Code requires no traditional installation - it's a conversational platform that you interact with directly. However, to integrate with various services and databases, you'll need to set up connectors.

### Prerequisites

Before using Calvin Code connectors, ensure you have:

- Access to your target services (Google Cloud, databases, etc.)
- Appropriate permissions to create service accounts or API keys
- Claude Code CLI (for MCP connector setup)

## Setting Up Claude Code

Calvin Code integrations work through the Claude Code CLI and Model Context Protocol (MCP).

### 1. Install Claude Code CLI

```bash
# macOS/Linux
curl -O https://storage.googleapis.com/genai-toolbox/v0.13.0/darwin/arm64/toolbox
chmod +x toolbox

# Or use the official Claude Code installation method
# Visit: https://docs.anthropic.com/en/docs/claude-code
```

### 2. Configure MCP Connectors

Add connectors using the Claude Code MCP system:

```bash
# Example: Adding BigQuery connector
claude mcp add-json bigquery-toolbox '{
  "command": "./toolbox",
  "args": ["--prebuilt", "bigquery", "--stdio"],
  "env": {
    "BIGQUERY_PROJECT": "your-project-id",
    "GOOGLE_APPLICATION_CREDENTIALS": "/path/to/service-account.json"
  }
}'
```

## Connector-Specific Setup

Each connector has its own setup requirements:

### Google Cloud Services (BigQuery, etc.)

1. **Enable APIs** in Google Cloud Console
2. **Create Service Account** with appropriate permissions
3. **Download JSON key** file
4. **Configure environment** variables

Detailed setup instructions: [BigQuery Connector](../connectors/bigquery.md)

### Database Connectors (PostgreSQL, MySQL)

1. **Database credentials** and connection information
2. **Network access** configuration
3. **Security settings** (SSL, firewall rules)

### API-based Connectors (Stripe, SendGrid)

1. **API keys** from service providers
2. **Webhook endpoints** configuration (if applicable)
3. **Rate limiting** considerations

## Verification

Test your setup by starting a conversation with Calvin Code:

```bash
# Start Claude Code
claude code

# In the conversation, test connector access
# Example: "Show me the tables in my BigQuery dataset"
```

## Environment Configuration

### Development Environment

For development, set up a local environment file:

```bash
# .env
BIGQUERY_PROJECT=your-dev-project
GOOGLE_APPLICATION_CREDENTIALS=./dev-service-account.json
DATABASE_URL=postgresql://user:pass@localhost/devdb
```

### Production Environment

For production deployments:

- Use secure secret management
- Implement proper access controls
- Configure monitoring and logging
- Set up backup and disaster recovery

## Troubleshooting

### Common Issues

**Authentication Errors**
- Verify service account permissions
- Check API key validity
- Ensure correct project/account selection

**Connection Timeouts**
- Check network connectivity
- Verify firewall settings
- Review rate limiting policies

**Permission Denied**
- Confirm service account roles
- Check database user permissions
- Verify API access levels

### Getting Help

1. Check connector-specific documentation
2. Review error logs and messages
3. Test with minimal configurations
4. [Contact support](https://www.gopersonal.com/es/contact) for complex issues

## Next Steps

Once your connectors are configured:

1. Explore the [Quick Start Guide](quickstart.md)
2. Review [Connector Documentation](../connectors/index.md)
3. Try [Example Use Cases](quickstart.md)

---

Ready to build your first AI-powered e-commerce solution? Let's get started with the [Quick Start Guide](quickstart.md)!