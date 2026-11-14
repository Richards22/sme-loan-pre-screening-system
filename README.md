# SME Loan Pre-Screening System

An AI-assisted loan pre-screening workflow built with **n8n, JavaScript, Groq, and Airtable**.

This portfolio project demonstrates how a financial-services workflow can automate repetitive first-stage application checks while keeping assessment and lending decisions under human control.

> **Important:** This is an educational demonstration. It does not approve or reject loans, determine creditworthiness, or replace a bank's credit assessment, compliance checks, or human review.

## Business Problem

Small and medium-sized businesses applying for finance may submit information that needs to be checked, organized, and reviewed before a Relationship Manager can assess the application.

Manual first-stage work can include:

- Checking whether required application details are present and valid.
- Calculating basic loan-to-revenue metrics.
- Detecting duplicate submissions.
- Recording application information and processing outcomes.
- Preparing preliminary assessment summaries.
- Notifying the relevant person when information is missing or a service fails.

These repetitive steps are suitable candidates for workflow automation.

## Solution

The workflow receives an application through an n8n webhook, validates the submitted data, calculates a basic financial ratio, checks Airtable for a duplicate submission reference, and routes the application according to the result.

For complete, non-duplicate applications, Groq generates a structured preliminary assessment using the supplied application data. The workflow records the outcome in Airtable and sends an email notification.

Incomplete applications and AI-service failures follow separate handling paths so that they can be recorded and followed up.

## Key Features

- **Webhook intake:** Receives application data through an HTTP POST request.
- **Data extraction and validation:** Extracts the business name, monthly revenue, requested loan amount, and submission reference; checks required values.
- **Financial metric:** Calculates the requested loan amount relative to monthly revenue.
- **Duplicate detection:** Searches Airtable using the submission reference.
- **AI-assisted assessment:** Sends eligible application data to the Groq API for a structured preliminary review.
- **Application recordkeeping:** Stores application details, screening status, AI assessment, and processing status in Airtable.
- **Email notifications:** Sends assessment results, missing-information notifications, and AI-service failure alerts.
- **Failure handling:** Routes AI request errors to a separate recordkeeping and notification path.

## Workflow Architecture

The main processing sequence is:

1. Receive an HTTP POST request at the `sme-loan-application` webhook.
2. Extract the submitted application fields.
3. Validate the business name, monthly revenue, and requested loan amount.
4. Calculate the loan-to-monthly-revenue ratio.
5. Search Airtable for an existing submission reference.
6. Stop duplicate submissions from proceeding through the normal assessment route.
7. Route incomplete applications to Airtable recordkeeping and a missing-information email.
8. Send complete, non-duplicate applications to the Groq API.
9. Save the AI assessment and application details in Airtable.
10. Format and send the assessment email.
11. If the AI request fails, save a failure record and send an alert.

## Screening Logic

The workflow uses JavaScript to validate the required inputs and calculate:

`Loan-to-monthly-revenue ratio = Requested loan amount / Monthly revenue`

The current demonstration applies these initial routing labels:

| Condition | Initial screening label |
|---|---|
| Required data is missing or invalid | Needs More Information |
| Ratio is 1 or below | Lower Initial Risk Flag |
| Ratio is above 1 and up to 3 | Manual Review Required |
| Ratio is above 3 | Higher Initial Risk Flag |

These are demonstration rules for workflow routing only. They are **not validated credit-risk thresholds** and should not be used to approve, decline, or price a real loan. Revenue alone does not establish cash flow, affordability, or repayment capacity.

## AI Assessment

The workflow calls the Groq chat-completions API using the `openai/gpt-oss-20b` model.

The prompt instructs the model to:

- Use only the application data supplied to it.
- Avoid inventing financial figures or application details.
- Distinguish supplied facts from assumptions.
- Explain limitations in the available information.
- Identify information a Relationship Manager should verify.
- Recommend human review or a request for additional information.
- Never state that a loan has been approved or rejected.

The AI output is a preliminary support document, not a credit decision.

## Technology Stack

| Technology | Role |
|---|---|
| n8n | Workflow orchestration and routing |
| JavaScript | Input validation, ratio calculation, and email formatting |
| Groq API | AI-assisted preliminary assessment |
| Airtable | Duplicate checks and application recordkeeping |
| Webhook / HTTP | Application intake and API communication |
| SMTP / Email | Notifications and assessment delivery |
| Docker | Local n8n environment |

## Example Webhook Payload

Send a POST request to the n8n webhook with JSON in this format:

```json
{
  "business_name": "Example Trading Company",
  "monthly_revenue": 5000000,
  "requested_loan_amount": 10000000,
  "submission_reference": "APP-2026-001"
}
```

Use fictional data when testing this demonstration. Do not submit real customer or applicant information to a public test environment.

## Repository Structure

```text
sme-loan-pre-screening-system/
├── workflow/
│   └── SME-Loan-Pre-Screening-v1-GitHub.json
├── screenshots/
├── documentation/
├── test-results/
├── README.md
└── .gitignore
```

The workflow JSON is a sanitized export intended for review and import into a separately configured n8n instance.

## Setup and Configuration

1. Install and run n8n. This project was developed using a local Docker-based n8n environment.
2. Import `workflow/SME-Loan-Pre-Screening-v1-GitHub.json`.
3. Create an Airtable base and table with fields that match the mappings in the workflow.
4. Configure your own Airtable credentials and replace the placeholder base and table references.
5. Configure Groq API authentication in n8n.
6. Configure the email/SMTP credentials and replace the example sender and recipient addresses.
7. Review the webhook path, node settings, and workflow connections.
8. Test the workflow with fictional application data before activating it.

The exported workflow is provided for configuration and review. It contains placeholders rather than the author's private credentials and is not intended to run immediately after import.

### Security

- Never commit API keys, passwords, access tokens, or private credentials.
- Do not commit real applicant information or personally identifiable information.
- Use fictional or anonymized data for portfolio demonstrations.
- Apply appropriate access controls before exposing a webhook publicly.

## Testing

The workflow was tested using these scenarios:

| Scenario | Expected workflow behavior |
|---|---|
| Valid application | Validate, calculate ratio, check duplicates, generate AI assessment, save record, and send email |
| Incomplete application | Record the issue and send a missing-information notification |
| Duplicate submission | Prevent the duplicate from proceeding through the normal assessment route |
| AI service failure | Save a failure record and send an alert |

Test evidence and detailed execution notes can be added to the `test-results/` and `screenshots/` folders.

## Limitations and Future Improvements

This is version 1 and intentionally has a limited screening scope. Potential improvements include:

- Stronger input validation and more robust duplicate handling.
- Additional financial information, such as operating costs, existing debt, cash flow, and repayment history.
- A dashboard for application status and operational reporting.
- More detailed audit logging and monitoring.
- Human review and approval stages with role-based access.
- Automated tests for edge cases and malformed requests.
- Production-grade authentication, privacy, and deployment controls.

## Disclaimer

This project is an educational portfolio demonstration. Its screening labels and AI-generated assessments are preliminary workflow outputs only. They are not financial advice, credit approval, rejection, or a substitute for a regulated lender's underwriting and compliance process.

## Author

Built as part of my journey into AI automation engineering, applying workflow automation and AI to practical business-process problems.
