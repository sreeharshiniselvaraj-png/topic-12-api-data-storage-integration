# Topic 12: API & Data Storage Integration
## Implementation Document

## 1. Implementation Summary
This document describes how the Topic 12 workflow is configured in n8n to retrieve user records from JSONPlaceholder and append selected values to Google Sheets.

## 2. Workflow Details
- **Workflow name:** Topic 12 API & Data Storage Integration Workflow
- **Workflow sequence:** Manual Trigger → Fetch API Users → Store Users in Sheet
- **Destination spreadsheet:** N8N API Storage Spreadsheet
- **Worksheet:** Sheet1

## 3. Google Sheets Preparation
1. Create a Google spreadsheet named `N8N API Storage Spreadsheet`.
2. Rename the worksheet tab to `Sheet1`.
3. Enter the following headers in row 1:
   - A1: `ID`
   - B1: `Name`
   - C1: `Email`
   - D1: `Company`
4. Ensure the Google Sheets credential configured in n8n has Editor access to the spreadsheet.
5. If using a service account, share the spreadsheet with the service-account email as an Editor.

## 4. Node Configuration

### 4.1 Manual Trigger
- Add the Manual Trigger node (shown in some n8n versions as **When clicking 'Test workflow'**).
- This node starts the workflow when the workflow is manually tested or executed.

### 4.2 Fetch API Users — HTTP Request
Configure the HTTP Request node as follows:

| Setting | Value |
|---|---|
| Node name | `Fetch API Users` |
| Method | `GET` |
| URL | `https://jsonplaceholder.typicode.com/users` |
| Authentication | `None` |

Leave query parameters, headers, and request body disabled/not configured because they are not required for this GET request.

Run **Test step** and inspect the output. The expected result is 10 user items. Each item includes flat properties such as `id`, `name`, and `email`, plus a nested `company` object.

Example response fragment:

```json
{
  "id": 1,
  "name": "Leanne Graham",
  "email": "Sincere@april.biz",
  "company": {
    "name": "Romaguera-Crona"
  }
}
```

### 4.3 Store Users in Sheet — Google Sheets
Add a Google Sheets node after `Fetch API Users` and configure it as follows:

| Setting | Value |
|---|---|
| Node name | `Store Users in Sheet` |
| Credential | Existing verified Google Sheets credential |
| Resource | Row (or the equivalent row resource in the installed n8n version) |
| Operation | Append Row / Append |
| Document | `N8N API Storage Spreadsheet` |
| Sheet | `Sheet1` |

Select the spreadsheet from the document dropdown or provide its Spreadsheet ID if that is the option available in the installed n8n version.

### 4.4 Column Mapping
Use manual column mapping and map each sheet header to the corresponding expression:

| Sheet column | Expression |
|---|---|
| ID | `{{ $json.id }}` |
| Name | `{{ $json.name }}` |
| Email | `{{ $json.email }}` |
| Company | `{{ $json.company.name }}` |

The `company.name` expression uses dot notation to access the nested `name` property inside `company`.

## 5. Execution Procedure
1. Save the workflow.
2. Confirm the node connections are in this order: Manual Trigger → Fetch API Users → Store Users in Sheet.
3. Confirm the destination spreadsheet has the four required headers.
4. Select **Test workflow** / **Execute workflow**.
5. Wait for the execution to finish.
6. Inspect the node execution results.
7. Open `N8N API Storage Spreadsheet` and verify the rows.

## 6. Expected Execution Result
- The HTTP Request node returns 10 user items.
- The Google Sheets node receives and processes the incoming items.
- 10 separate data rows are appended below the header row.
- The ID, Name, and Email columns contain the corresponding API values.
- The Company column contains each user's nested `company.name` value.
- For the first user, the Company value is `Romaguera-Crona`.

n8n handles the incoming items through its normal multi-item execution behavior. An additional Loop Over Items node is not required for this workflow.

## 7. Verification Checklist
- [ ] Workflow saved with the required name.
- [ ] Manual Trigger is connected to `Fetch API Users`.
- [ ] `Fetch API Users` is connected to `Store Users in Sheet`.
- [ ] HTTP method is GET.
- [ ] API URL is correct.
- [ ] HTTP Request output contains 10 items.
- [ ] Google Sheets credential is selected and valid.
- [ ] Append Row operation is selected.
- [ ] Correct spreadsheet and `Sheet1` are selected.
- [ ] ID, Name, Email, and Company mappings are correct.
- [ ] The nested expression `{{ $json.company.name }}` resolves correctly.
- [ ] 10 data rows are present in the spreadsheet after one successful run.

## 8. Operational Note
The Google Sheets Append Row operation adds new rows each time the workflow is executed. Avoid running the workflow repeatedly against the same populated sheet unless duplicate rows are intended or the previous test rows have been cleared.

## 9. Completion
The implementation is complete when the workflow successfully retrieves the 10 sample users and appends all 10 records to the configured Google Sheet with the nested company names correctly populated.
