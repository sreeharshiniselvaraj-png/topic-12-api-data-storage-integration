# Topic 12: API & Data Storage Integration
## Requirements Document

## 1. Purpose
This document defines the requirements for an n8n workflow that retrieves user records from a public REST API and stores selected fields in Google Sheets.

## 2. Objective
The objective is to teach trainees how to:
- Configure an HTTP Request node to make a GET request.
- Retrieve an array of JSON user records from an external API.
- Configure Google Sheets to append incoming records.
- Map flat JSON properties and a nested JSON property using expressions.
- Use n8n's native multi-item processing to write all returned records.

## 3. Scope

### 3.1 In Scope
- Fetching user records from `https://jsonplaceholder.typicode.com/users`.
- Using the HTTP Request node with the GET method.
- Appending records to a Google Sheets worksheet.
- Mapping `id`, `name`, `email`, and `company.name`.
- Processing the 10 records returned by the sample API.

### 3.2 Out of Scope
- Deduplication or checking whether a record already exists.
- Updating existing spreadsheet rows.
- Creating spreadsheet tabs, columns, or formatted tables through the Google Sheets API.
- Writing data to SQL databases or other storage systems.

## 4. Target Audience and Prerequisites
The intended audience is n8n trainees or employees who have completed the preceding course topics.

Prerequisites:
- Access to an n8n workspace.
- Internet access to reach the JSONPlaceholder API.
- A valid Google Sheets credential configured in n8n.
- A Google spreadsheet accessible to that credential.
- A worksheet with the exact headers `ID`, `Name`, `Email`, and `Company`.

## 5. Workflow Overview
The workflow shall execute in this order:

**Manual Trigger → Fetch API Users → Store Users in Sheet**

## 6. Functional Requirements

| Requirement ID | Requirement | Priority |
|---|---|---|
| REQ-001 | The workflow shall use an HTTP Request node named `Fetch API Users` to perform a GET request to `https://jsonplaceholder.typicode.com/users`. | High |
| REQ-002 | The workflow shall use a Google Sheets node named `Store Users in Sheet` with the Append Row operation. | High |
| REQ-003 | The Google Sheets node shall map `id` to ID, `name` to Name, `email` to Email, and nested `company.name` to Company. | High |
| REQ-004 | The workflow shall process all 10 user items returned by the API and append each item as a separate spreadsheet row. | High |
| REQ-005 | The Google Sheets node shall target the spreadsheet `N8N API Storage Spreadsheet` and the `Sheet1` worksheet. | High |
| REQ-006 | The workflow shall execute sequentially from Manual Trigger to HTTP Request to Google Sheets and complete the data transfer successfully. | High |

## 7. Technical Requirements

| Requirement ID | Technical requirement | Details |
|---|---|---|
| TR-001 | Automatic multi-item processing | n8n shall pass the multiple items returned by the HTTP Request node to the downstream Google Sheets node for processing. |
| TR-002 | Nested property resolution | The expression `{{ $json.company.name }}` shall resolve the company name from each user's nested `company` object. |
| TR-003 | Multiple-row insertion | The Google Sheets node shall append the 10 incoming records without an additional Loop Over Items node for this exercise. |

## 8. Data Mapping Requirements

| Sheet header | Source property | Expression |
|---|---|---|
| ID | `id` | `{{ $json.id }}` |
| Name | `name` | `{{ $json.name }}` |
| Email | `email` | `{{ $json.email }}` |
| Company | `company.name` | `{{ $json.company.name }}` |

## 9. Assumptions and Constraints
- The JSONPlaceholder endpoint returns an array of 10 structured sample user records.
- The first row of `Sheet1` contains the exact required headers.
- The Google Sheets credential has permission to append rows to the target spreadsheet.
- This exercise is limited to 10 sample records.
- Re-running an Append Row workflow can create duplicate rows; deduplication is not part of this exercise.

## 10. Acceptance Criteria
The workflow is acceptable when:
1. The workflow is saved with the required name: `Topic 12 API & Data Storage Integration Workflow`.
2. The HTTP Request node uses GET and the specified endpoint.
3. The API response contains 10 user records.
4. The Google Sheets node uses Append Row and targets the required spreadsheet and worksheet.
5. All four fields are mapped correctly, including the nested company name.
6. One successful end-to-end execution appends 10 data rows to the sheet.
