# SME Loan Pre-Screening System

An AI-assisted loan pre-screening workflow built with **n8n, Groq, and Airtable**.

## Overview

Small and medium-sized businesses often need access to financing, but reviewing loan applications manually can be time-consuming.

This project demonstrates how workflow automation and AI can support the initial review of SME loan applications by collecting application data, validating required information, calculating basic loan metrics, identifying duplicate submissions, and generating an AI-assisted preliminary assessment.

**Important:** This is a demonstration and pre-screening tool. It does not make final lending decisions or replace a bank's credit risk assessment, compliance checks, or human review.

## Business Problem

Manual loan application screening can involve repetitive tasks such as:

- Checking whether applicants have submitted all required information.
- Calculating basic financial ratios.
- Identifying duplicate applications.
- Preparing preliminary assessment reports.
- Communicating missing information to applicants.

These activities can be streamlined through automation.

## Solution

The workflow automates several stages of the initial loan application process.

### Key Features

- **Application intake:** Receives loan application data through a webhook.
- **Data validation:** Checks whether required application information is available.
- **Financial calculation:** Calculates the requested loan amount relative to monthly revenue.
- **Duplicate detection:** Checks whether an application reference already exists.
- **AI-assisted assessment:** Uses Groq to generate a preliminary assessment based on the supplied application data.
- **Airtable recordkeeping:** Saves application information and processing outcomes.
- **Email notifications:** Sends assessment results, missing-information requests, and AI service failure notifications.

## Technology Stack

| Technology | Purpose |
|---|---|
| n8n | Workflow automation |
| Groq API | AI-assisted application assessment |
| Airtable | Application records and tracking |
| Webhook | Application submission endpoint |
| SMTP / Email | Applicant notifications |
| Docker | Local n8n environment |

## Workflow Architecture

1. Receive loan application through webhook.
2. Extract and validate application data.
3. Calculate the loan-to-monthly-revenue ratio.
4. Search Airtable for an existing submission reference.
5. Identify duplicate applications.
6. Check application completeness.
7. Route incomplete applications for follow-up.
8. Send complete applications for AI-assisted assessment.
9. Save the assessment and application record in Airtable.
10. Format and send the assessment email.
11. Handle AI service failures separately and record the failure.

## Repository Structure

```text
sme-loan-pre-screening-system/
│
├── workflow/
│   └── sme-loan-pre-screening-v1.json
│
├── screenshots/
├── documentation/
├── test-results/
├── README.md
└── .gitignore
```

## Setup and Configuration

This repository contains a sanitized n8n workflow export.

To use it:

1. Install and run n8n.
2. Import the JSON file from the `workflow` folder.
3. Configure your own Airtable credentials and base/table settings.
4. Configure your own Groq API credentials.
5. Configure your email/SMTP credentials.
6. Review the workflow settings and webhook configuration.
7. Test the workflow with sample application data before using it.

**Security:** Never commit API keys, passwords, access tokens, or private applicant information to this repository.

## Testing

The workflow was tested against the following scenarios:

- Valid loan application.
- Incomplete loan application.
- AI service failure.
- Duplicate application submission.

Test evidence and detailed results will be added to the `test-results` and `screenshots` folders.

## Limitations and Future Improvements

This is version 1 of the project. Potential improvements include:

- A loan application dashboard.
- More comprehensive financial eligibility checks.
- Application status tracking.
- Audit trails and reporting.
- Additional validation and security controls.
- Human review and approval stages.

## Disclaimer

This project is an educational portfolio demonstration. Its AI-generated assessments are preliminary and should not be treated as financial advice, credit approval, or an actual lending decision.

## Author

Built as part of my journey into AI automation engineering, with a focus on solving practical business problems through automation and AI.