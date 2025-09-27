# Aurora Mortgage Processor

A comprehensive MuleSoft application that automates mortgage document processing using Intelligent Document Processing (IDP), Salesforce integration, and automated notifications.

## 🏠 Overview

The Aurora Mortgage Processor is an intelligent document processing system that automatically extracts key information from mortgage application documents, validates the data, creates complete deals in Salesforce (Account + Contact + Opportunity), and sends notifications via email and Slack. The system is designed to streamline mortgage application workflows and reduce manual processing time by creating full sales opportunities rather than just leads.

## 🚀 Key Features

### 📄 **Intelligent Document Processing**
- **Automated Document Detection**: Monitors S3 bucket for new mortgage documents
- **IDP Integration**: Uses MuleSoft's Intelligent Document Processing to extract structured data
- **Data Validation**: Validates extracted information including names, emails, postal codes, and identifiers
- **Error Detection**: Identifies missing or invalid data with detailed error reporting

### 🔄 **Automated Workflow**
- **S3 Trigger**: Automatically processes documents when uploaded to designated S3 folder
- **OAuth Authentication**: Secure authentication with MuleSoft Anypoint Platform
- **Asynchronous Processing**: Polls IDP service until document processing is complete
- **Error Handling**: Comprehensive error handling with detailed logging

### 📧 **Multi-Channel Notifications**
- **Email Notifications**: Sends formatted HTML emails to applicants with validation errors
- **Slack Integration**: Real-time notifications to team channels for processing status
- **Review Workflow**: Direct links to Salesforce opportunities for team review
- **Success/Failure Tracking**: Different notification formats for successful processing vs. errors

### 🎯 **Salesforce Deal Creation**
- **Complete Deal Creation**: Automatically creates Account, Contact, and Opportunity for successful applications
- **Account Management**: Creates customer accounts with industry classification and rating
- **Contact Management**: Creates contacts with proper account relationships and lead source tracking
- **Opportunity Management**: Creates sales opportunities with stage, probability, and close date
- **Data Mapping**: Maps extracted document data to appropriate Salesforce object fields
- **Duplicate Prevention**: Uses external IDs to prevent duplicate record creation

## 🏗️ Architecture

### **Core Components**

1. **S3 New Object Listener**
   - Monitors S3 bucket for new documents in mortgage folder
   - Triggers processing workflow automatically

2. **IDP Integration**
   - OAuth 2.0 authentication with MuleSoft Anypoint Platform
   - Document submission to IDP service
   - Polling mechanism for processing completion

3. **Data Extraction & Validation**
   - Parses IDP response to extract structured data
   - Validates required fields (name, email, postal code, identifier)
   - Handles JSON parsing with proper error handling

4. **Salesforce Deal Creation**
   - Creates Account with customer information and industry classification
   - Creates Contact with proper account relationship and lead source tracking
   - Creates Opportunity with stage, probability, amount, and close date
   - Uses original email from document for contact creation
   - Generates unique external IDs for all objects to prevent duplicates

5. **Notification System**
   - HTML email notifications for validation errors
   - Slack notifications for processing status
   - Formatted messages with document details

### **Data Flow**

```
S3 Document Upload → IDP Processing → Data Extraction → Validation → 
├─ Success: Salesforce Lead Creation + Slack Success Notification
└─ Errors: Email to Applicant + Slack Error Notification
```

## 📋 Extracted Data Fields

The system extracts and validates the following information from mortgage documents:

- **Personal Information**
  - First Name
  - Last Name
  - Email Address
  - Postal Code (Canadian/US format validation)
  - Identifier (SIN/SSN with 9-digit validation)
  - Application Date

- **Validation Rules**
  - Email format validation
  - Postal code format (A1A 1A1 or 12345/12345-6789)
  - Identifier length validation (exactly 9 digits)
  - Date format validation (MM/DD/YYYY, YYYY-MM-DD, MMM DD, YYYY)

## 🛠️ Technical Stack

### **MuleSoft Components**
- **Mule Runtime**: 4.9.9
- **Connectors Used**:
  - Amazon S3 Connector (7.2.0)
  - Email Connector (1.7.6)
  - Salesforce Connector (11.2.1)
  - HTTP Connector (1.10.3)
  - ObjectStore Connector (1.2.2)
  - Slack Connector (TBD - Planned Enhancement)

### **External Integrations**
- **AWS S3**: Document storage and monitoring
- **MuleSoft IDP**: Intelligent document processing
- **Salesforce**: Lead management
- **Gmail SMTP**: Email notifications
- **Slack API**: Team notifications

### **Data Processing**
- **DataWeave 2.0**: Data transformation and mapping
- **JSON Processing**: Complex JSON parsing and validation
- **Multipart Form Data**: Document upload handling

## ⚙️ Configuration

### **Mule Application Packaging**

> **Note:** As of v1.4.0, the Maven build now explicitly includes all Mule configuration files (`src/main/mule/*.xml`) in the application JAR. This ensures files like `global.xml` are always available at runtime. If you add new Mule config files, rebuild the project to include them.

### **Required Properties** (`config.properties`)

```properties
# HTTP Configuration
http.listener.port=8081
http.host=slack.com
http.port=443
http.protocol=HTTPS

# S3 Configuration
s3.accessKey=your-s3-access-key
s3.secretKey=your-s3-secret-key
s3.region=us-east-1
s3.bucket=your-bucket-name
s3.mortgage.folder=mortgage

# Salesforce Configuration
sfdc.username=your-salesforce-username
sfdc.password=your-salesforce-password

# IDP Configuration
idp.oauth.host=anypoint.mulesoft.com
idp.oauth.protocol=HTTPS
idp.rt.host=idp-rt.us-east-1.anypoint.mulesoft.com
idp.rt.protocol=HTTPS
idp.organization.id=your-org-id
idp.action.id=your-action-id

# Salesforce Deal Creation Configuration
# Account Configuration
account.name.prefix=AURORA-
account.name.suffix=-MORTGAGE
account.type=Customer
account.industry=Financial Services
account.rating=Hot

# Contact Configuration
contact.lead.source=Document Upload
contact.phone.default=+1-555-000-0000
contact.title.default=Mortgage Applicant

# Opportunity Configuration
opportunity.name.prefix=MORTGAGE-
opportunity.name.suffix=-DEAL
opportunity.stage.name=Prospecting
opportunity.type=New Business
opportunity.lead.source=Document Upload
opportunity.probability=10
opportunity.amount.default=500000
opportunity.close.date.days=30
idp.client.id=your-client-id
idp.client.secret=your-client-secret

# Slack Configuration
slack.channel.name=your-channel-name
slack.bot.token=your-slack-bot-token

# Email Configuration
email.smtp.host=smtp.gmail.com
email.smtp.port=587
email.smtp.username=your-email@gmail.com
email.smtp.password=your-app-password
email.from.address=your-email@gmail.com
email.from.name=Aurora Mortgage Processor
default.test.email=test@example.com
```

## 🚀 Deployment

### **Prerequisites**
- MuleSoft Anypoint Studio
- Mule Runtime 4.9.9
- Java 17
- Access to required external services (S3, Salesforce, Slack, Gmail)

### **Build & Deploy**
```bash
# Build the application (includes all Mule XML configs)
mvn clean package

# Deploy to Mule Runtime (ensure Mule is running)
cp target/aurora-mortgage-processor-1.0.0-mule-application.jar /path/to/mule/apps/
```

### **Runtime Requirements**
- **Memory**: Minimum 2GB RAM
- **Storage**: 1GB for application and logs
- **Network**: HTTPS access to external services

## 📊 Monitoring & Logging

### **Log Levels**
- **INFO**: Processing status, successful operations
- **WARN**: Validation errors, non-critical issues
- **ERROR**: Processing failures, system errors

### **Key Log Messages**
- Document detection and processing status
- IDP authentication and submission results
- Data extraction and validation results
- Salesforce lead creation status
- Email and Slack notification delivery

### **Monitoring Points**
- S3 document detection frequency
- IDP processing success rate
- Salesforce lead creation success rate
- Email delivery status
- Slack notification delivery

## 🔧 Error Handling

### **Document Processing Errors**
- **Invalid Document Format**: Logs error and sends Slack notification
- **IDP Processing Failure**: Retries with exponential backoff
- **Data Extraction Errors**: Detailed error logging with context

### **Integration Errors**
- **Salesforce Connection Issues**: Automatic retry with error logging
- **Email Delivery Failures**: Fallback notification via Slack
- **Slack API Errors**: Logged for manual follow-up

### **Validation Errors**
- **Missing Required Fields**: Email sent to applicant with specific error details
- **Invalid Data Formats**: Detailed validation messages in email
- **Data Quality Issues**: Comprehensive error reporting

## 📈 Performance Considerations

### **Optimization Features**
- **Asynchronous Processing**: Non-blocking document processing
- **Efficient Polling**: 2-second intervals with 30-second timeout
- **Connection Pooling**: Reused connections for external services
- **Memory Management**: Efficient data transformation and cleanup

### **Scalability**
- **Horizontal Scaling**: Multiple Mule instances supported
- **Load Balancing**: S3 event distribution across instances
- **Resource Management**: Configurable connection pools and timeouts

## 🔒 Security

### **Authentication & Authorization**
- **OAuth 2.0**: Secure authentication with MuleSoft Anypoint Platform
- **API Keys**: Secure storage of external service credentials
- **TLS/SSL**: Encrypted communication with all external services

### **Data Protection**
- **Sensitive Data**: Secure handling of personal information
- **Credential Management**: Encrypted storage of passwords and tokens
- **Audit Logging**: Comprehensive logging for compliance

## 🧪 Testing

### **Test Scenarios**
- **Valid Document Processing**: End-to-end successful workflow
- **Invalid Document Handling**: Error scenarios and notifications
- **Integration Testing**: External service connectivity
- **Data Validation**: Field validation and error handling

### **Test Data**
- Sample mortgage documents with various data quality scenarios
- Mock responses for external service testing
- Validation test cases for all data fields

## 📝 API Documentation

### **S3 Integration**
- **Trigger**: New object creation in designated folder
- **Payload**: S3 object metadata including key and bucket information
- **Response**: Processing status and results

### **IDP Integration**
- **Authentication**: OAuth 2.0 client credentials flow
- **Document Submission**: Multipart form data with document content
- **Status Polling**: GET requests for processing status

### **Salesforce Integration**
- **Lead Creation**: Upsert operation with external ID
- **Data Mapping**: Document data to Salesforce lead fields
- **Error Handling**: Comprehensive error response handling

## 🔄 Current Integration Status

### **Slack Integration (v1.0.0)**
- **Current Implementation**: HTTP REST API calls to Slack
- **Features**: 
  - Success notifications with direct links to Salesforce opportunities
  - Error notifications for processing failures
  - Readable date formatting (e.g., "September 26, 2025")
  - Review workflow with "Start Review" button
- **Message Format**: Clean, actionable notifications for team review

### **Planned Enhancements**
- **Slack Connector Integration**: Replace HTTP calls with native Slack connector
- **Benefits**: 
  - Simplified configuration and authentication
  - Built-in error handling and retry logic
  - Type-safe operations and parameters
  - Reduced code complexity

## 🤝 Contributing

### **Development Guidelines**
- Follow MuleSoft best practices for flow design
- Use meaningful variable and flow names
- Implement comprehensive error handling
- Add detailed logging for debugging

### **Code Standards**
- Consistent XML formatting and indentation
- Descriptive DataWeave expressions
- Proper error handling and logging
- Documentation for complex transformations

## 📞 Support

For technical support or questions about the Aurora Mortgage Processor:

- **Documentation**: Refer to this README and inline code comments
- **Logs**: Check Mule Runtime logs for detailed error information
- **Monitoring**: Use Anypoint Monitoring for runtime metrics
- **Troubleshooting**: Follow error handling patterns in the code

## 📄 License

This project is proprietary software developed for Aurora Mortgage processing workflows.

---

**Version**: 1.4.0  
**Last Updated**: September 2025  
**Mule Runtime**: 4.9.9  
**Java Version**: 17