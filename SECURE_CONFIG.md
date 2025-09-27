# Secure Configuration Setup

This application uses secure property placeholders to protect sensitive information like API keys, passwords, and tokens.

## Configuration Files

The main configuration uses placeholders in the following format:
- `${secure::property.name}` for secure properties

## Setting up Secure Properties for Local Development

1. Create a `secure-config.properties` file in `src/main/resources/` (this file is gitignored):

```properties
# S3 Configuration
s3.accessKey=YOUR_AWS_ACCESS_KEY
s3.secretKey=YOUR_AWS_SECRET_KEY

# Slack Configuration  
slack.bot.token=YOUR_SLACK_BOT_TOKEN

# Salesforce Configuration
sfdc.username=YOUR_SFDC_USERNAME
sfdc.password=YOUR_SFDC_PASSWORD

# Email Configuration
email.smtp.username=YOUR_EMAIL_USERNAME
email.smtp.password=YOUR_EMAIL_PASSWORD

# IDP Configuration
idp.client.id=YOUR_IDP_CLIENT_ID
idp.client.secret=YOUR_IDP_CLIENT_SECRET
idp.organization.id=YOUR_IDP_ORG_ID
idp.action.id=YOUR_IDP_ACTION_ID
```

## CloudHub Deployment

When deploying to CloudHub, set these properties as secure properties in the Runtime Manager:

1. Go to Runtime Manager
2. Select your application
3. Go to Settings > Properties
4. Add each secure property with the `secure::` prefix
5. Mark them as secure/hidden

## Environment Variables

Alternatively, you can set these as environment variables and reference them in your configuration files.
