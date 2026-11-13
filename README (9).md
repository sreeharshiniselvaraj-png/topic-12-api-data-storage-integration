# Topic 12: API & Data Storage Integration

## Overview
This n8n workflow retrieves user profile records from the JSONPlaceholder public API and appends the returned records to a Google Sheets spreadsheet.

## Workflow
Manual Trigger → Fetch API Users → Store Users in Sheet

## Tools and Services
- **Automation platform:** n8n
- **API:** JSONPlaceholder Users endpoint
- **HTTP method:** GET
- **Storage:** Google Sheets
- **Google Sheets operation:** Append Row

## API Endpoint
`https://jsonplaceholder.typicode.com/users`

The endpoint returns 10 sample user records in JSON format.

## Google Sheets Setup
Create a spreadsheet named **N8N API Storage Spreadsheet** with a sheet tab named **Sheet1**. The first row must contain these exact headers:

| Column | Header |
|---|---|
| A | ID |
| B | Name |
| C | Email |
| D | Company |

The Google Sheets credential used by n8n must have permission to access and edit the spreadsheet. If using a Google service account, share the spreadsheet with the service-account email as an Editor.

## Node Configuration Summary

### 1. Manual Trigger
Starts the workflow when the user selects the test/execute workflow action.

### 2. Fetch API Users (HTTP Request)
- Method: `GET`
- URL: `https://jsonplaceholder.typicode.com/users`
- Authentication: `None`

Expected output: 10 user items.

### 3. Store Users in Sheet (Google Sheets)
- Resource: Row (or the equivalent row resource in the installed n8n version)
- Operation: Append Row / Append
- Document: `N8N API Storage Spreadsheet`
- Sheet: `Sheet1`
- Credential: The configured Google Sheets credential

## Column Mapping

| Google Sheets column | n8n expression |
|---|---|
| ID | `{{ $json.id }}` |
| Name | `{{ $json.name }}` |
| Email | `{{ $json.email }}` |
| Company | `{{ $json.company.name }}` |

The Company expression uses dot notation to read the nested `name` property inside the API response's `company` object.

## Expected Result
After one successful workflow execution:
- The HTTP Request node returns 10 user items.
- Google Sheets appends one row for each incoming item.
- The spreadsheet contains 10 data rows beneath the header row.
- The Company column contains the corresponding company names.

n8n processes the incoming items through the Google Sheets node automatically. A separate Loop Over Items node is not required for this exercise.

## How to Run
1. Confirm the Google Sheets credential is valid.
2. Confirm the spreadsheet and `Sheet1` exist with the required headers.
3. Open the workflow in n8n.
4. Select **Test workflow** / **Execute workflow**.
5. Confirm the nodes complete successfully.
6. Open the spreadsheet and verify the 10 appended records.

**Note:** The Append Row operation adds new rows on every execution. Avoid repeated test runs against the same sheet unless duplicate rows are acceptable or the sheet has been cleared first.

## Validation
See `REQUIREMENTS.md` for the functional and technical requirements, and `IMPLEMENTATION.md` for the node setup and field mapping.
