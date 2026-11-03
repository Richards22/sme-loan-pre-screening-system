# SME Loan Pre-Screening System — Workflow Architecture

## 1. Introduction

This document explains the architecture and processing logic of the SME Loan Pre-Screening System v1.

The system uses n8n to coordinate application intake, data validation, financial calculations, duplicate detection, AI-assisted assessment, recordkeeping, and email notifications.

## 2. High-Level Architecture

The workflow consists of five main stages:

1. Application intake and data preparation.
2. Validation and financial calculations.
3. Duplicate detection and application routing.
4. AI-assisted assessment and recordkeeping.
5. Email notifications and error handling.

## 3. Workflow Components

### Stage 1: Application Intake

**Nodes:**
- Receive Loan Application
- Extract Application Data

The workflow receives loan application information through an n8n webhook.

The extraction step prepares the submitted information for subsequent processing.

Key fields include:
- Business name
- Monthly revenue
- Requested loan amount
- Submission reference

### Stage 2: Validation and Financial Calculations

**Node:**
- Validate and Calculate Loan Metrics

This stage checks the required application information and calculates a basic financial metric.

The loan-to-monthly-revenue ratio is calculated as:

`Requested Loan Amount / Monthly Revenue`

The workflow also produces a preliminary screening status based on its configured logic.

This ratio is an initial screening indicator, not a complete creditworthiness assessment.

### Stage 3: Duplicate Detection and Application Routing

**Nodes:**
- Check Existing Application
- Preserve Application Data
- Check Duplicate Submission
- Check Application Completeness

The workflow searches Airtable using the submission reference.

A Merge node preserves the original application data alongside the Airtable lookup result.

The duplicate-checking step determines whether the submission has already been recorded.

- Duplicate submission: routed to the duplicate branch.
- New submission: proceeds to the completeness check.

The completeness check then separates applications into two paths:

- Incomplete applications are saved and routed for missing-information notification.
- Complete applications proceed to AI assessment.

### Stage 4: AI-Assisted Assessment

**Node:**
- Generate AI Loan Assessment

Complete applications are sent to the Groq API.

The AI model receives the available application information and generates a preliminary assessment.

The assessment is intended to support initial screening. It does not independently approve or reject a loan.

### Stage 5: Recordkeeping and Notifications

**Nodes:**
- Save Completed Application
- Format AI Assessment Email
- Send Assessment Email
- Save Incomplete Application
- Send Missing Information Email

The workflow stores application records in Airtable.

The assessment response is formatted before being sent through email.

Incomplete applications are also saved, with a separate notification requesting the missing information.

## 4. Error Handling

**Nodes:**
- Save AI Failure Record
- Send AI Failure Notification

The AI assessment request has a dedicated error-handling path.

If the AI service request fails, the workflow:

1. Routes the failure through the error output.
2. Saves an AI failure record in Airtable.
3. Sends a separate failure notification.
4. Avoids sending the normal AI assessment email.

This provides visibility into failed processing attempts.

## 5. Data Storage

Airtable serves as the workflow's application recordkeeping layer.

It supports:
- Storing submitted application information.
- Checking submission references for duplicates.
- Recording application processing outcomes.
- Keeping a record of AI assessment failures.

## 6. External Services

| Service | Responsibility |
|---|---|
| n8n | Workflow orchestration |
| Groq API | AI-assisted assessment |
| Airtable | Application recordkeeping and duplicate lookup |
| SMTP / Email | Notifications |
| Docker | Local hosting environment |

## 7. Design Considerations

The workflow was designed around several practical automation principles:

- **Data validation:** Check required information before AI processing.
- **Duplicate prevention:** Avoid repeated processing of the same submission reference.
- **Data preservation:** Retain original application information when combining it with lookup results.
- **Exception handling:** Separate normal processing from AI service failures.
- **Traceability:** Save application and failure records.
- **Human oversight:** Treat AI assessments as preliminary outputs requiring appropriate review.

## 8. Current Limitations

Version 1 is a portfolio demonstration and has not been designed or approved as a production lending system.

Potential improvements include:
- Stronger input validation and authentication.
- More comprehensive financial analysis.
- Application status tracking.
- Audit logging and monitoring.
- Human review and approval workflows.
- Production-grade security and access controls.

## 9. Conclusion

The project demonstrates how workflow automation, API integration, data validation, AI services, and exception handling can be combined to streamline an initial business process.

It provides a foundation for more advanced financial-services automation projects.