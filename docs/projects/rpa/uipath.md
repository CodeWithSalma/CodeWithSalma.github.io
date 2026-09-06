# UiPath Invoice Processing Automation

An end-to-end RPA solution that automates vendor invoice processing — from Gmail retrieval to a consolidated Excel tracking report — built with UiPath and deployed through UiPath Orchestrator.

---

## The Problem

Manual invoice processing is repetitive and error-prone:

* Downloading email attachments one by one
* Opening and reading each invoice manually
* Copying data into Excel by hand
* Checking for missing or invalid entries

## The Solution

This automation eliminates manual handling by reading vendor invoices directly from a Gmail inbox, processing **PDF**, **Excel**, and **JSON** attachments, extracting and validating structured data, and appending everything into a centralized master Excel report — with zero manual data entry.

---

## Technology Stack

| Technology                    | Purpose                                          |
| ----------------------------- | ------------------------------------------------ |
| UiPath Studio                 | RPA workflow development                         |
| UiPath Orchestrator           | Automation deployment, execution, and monitoring |
| UiPath Mail Activities        | Gmail email retrieval                            |
| UiPath Excel Activities       | Excel file processing and reporting              |
| UiPath PDF Activities         | PDF text extraction                              |
| UiPath Document Understanding | Document processing and OCR                      |
| UiPath Intelligent OCR        | OCR processing for scanned documents             |
| UiPath GSuite Activities      | Gmail integration                                |
| Gmail IMAP                    | Email and attachment retrieval                   |
| NPOI Library                  | Excel document processing                        |
| VB.NET                        | Data manipulation and workflow logic             |
| Regular Expressions           | Invoice field extraction and parsing             |

---

## Architecture

The robot processes each Gmail attachment through a type-specific branch before converging into a single validation and reporting stage.

```text
Vendor → Gmail Inbox → Download Attachments
                              │
                ┌─────────────┼─────────────┐
                ▼             ▼             ▼
              PDF           Excel          JSON
                │             │             │
        Read PDF Text   Read Workbook  Read JSON Text
                │             │             │
        Parse Invoice    Clean Data    Parse Invoice
                │             │             │
                └─────────────┼─────────────┘
                              ▼
                    Validate & Calculate Total
                              ▼
                    Append to Master Excel
```

### Processing Branches

| File Type | Processing Steps                                          |
| --------- | --------------------------------------------------------- |
| PDF       | Read PDF text → parse invoice fields via regex → validate |
| Excel     | Read workbook → clean raw data → validate                 |
| JSON      | Read JSON text → parse invoice fields → clean → validate  |

### Data Extracted Per Invoice

* Vendor Name
* Customer Name
* Invoice Number
* Invoice Date
* Due Date
* Description
* Quantity
* Unit Price
* Total Amount
* Payment Status

---

## Key Capabilities

| Capability             | Description                                                |
| ---------------------- | ---------------------------------------------------------- |
| Email Retrieval        | Reads vendor invoice emails via Gmail IMAP                 |
| Multi-format Parsing   | Handles PDF, Excel, and JSON attachments                   |
| Data Extraction        | Pulls invoice number, dates, amounts, and customer details |
| Validation & Cleaning  | Standardizes formats, trims data, and flags missing values |
| Consolidated Reporting | Appends all records into one master Excel file             |
| Enterprise Deployment  | Published and executed via UiPath Orchestrator             |

---

## Data Processing & Validation

Before any record is written to the master Excel file, the robot applies the following checks:

* Trim unnecessary spaces
* Standardize customer names
* Convert date format
* Convert currency to numeric value
* Calculate total amount
* Detect missing values
* Highlight invalid data

### Output Columns

The master Excel tracking file contains:

| Column         |
| -------------- |
| Invoice Number |
| Invoice Date   |
| Customer       |
| Description    |
| Quantity       |
| Unit Price     |
| Total          |
| Status         |

---

## Setup & Configuration

### Prerequisites

* UiPath Studio
* Microsoft Excel
* Gmail Account
* Internet connection

### Required UiPath Packages

| Package                                 | Purpose           |
| --------------------------------------- | ----------------- |
| UiPath.Mail.Activities                  | Gmail integration |
| UiPath.Excel.Activities                 | Excel processing  |
| UiPath.PDF.Activities                   | PDF reading       |
| UiPath.System.Activities                | Core activities   |
| UiPath.DocumentUnderstanding.Activities | OCR               |
| UiPath.IntelligentOCR.Activities        | Intelligent OCR   |
| UiPath.GSuite.Activities                | Gmail activities  |

Install these packages through **Manage Packages** in UiPath Studio.

### Gmail Configuration

1. **Enable IMAP** — Gmail → Settings → Forwarding and POP/IMAP → Enable IMAP
2. **Enable Two-Factor Authentication** — Google Account → Security → 2-Step Verification
3. **Generate an App Password** — Google Account → Security → App Passwords → Generate Password, then save it for UiPath authentication

### Configure Variables

| Variable          | Example             |
| ----------------- | ------------------- |
| Gmail Address     | `example@gmail.com` |
| App Password      | `********`          |
| IMAP Server       | `imap.gmail.com`    |
| Port              | `993`               |
| Mail Folder       | `INBOX`             |
| Attachment Folder | `Input/`            |

---

## Running the Project

1. Clone the repository
2. Open the project in UiPath Studio
3. Install missing packages
4. Configure Gmail credentials and the variables above
5. Run `Main.xaml`
6. Wait until processing completes
7. Check the generated Excel report

---

## Deployment

The automation has been published and executed through **UiPath Orchestrator**, demonstrating readiness for enterprise RPA environments beyond local development.

### Deployment Activities

* Published the automation package to Orchestrator
* Created the process in Orchestrator
* Executed jobs from Orchestrator
* Monitored execution logs
* Verified successful end-to-end automation runs

---

## Troubleshooting

### Gmail Authentication Failed

* Enable IMAP
* Use an App Password instead of the regular Gmail password
* Verify Gmail credentials

### No Emails Found

* Check the mailbox folder
* Verify the email subject filter
* Ensure unread emails are available

### PDF Cannot Be Read

* Verify the PDF is not corrupted
* Use OCR for scanned documents

### Excel Cannot Be Opened

* Close the Excel file before running
* Verify the file path
* Check write permissions

### Missing UiPath Activities

* Open **Manage Packages**
* Install all required packages

---

## Business Impact

* Eliminates repetitive manual invoice entry
* Reduces human error in data consistency
* Enables centralized monitoring and job execution via Orchestrator
* Demonstrates enterprise-ready RPA deployment beyond local development

---

## Future Enhancements

* OCR for scanned invoices
* AI Document Understanding
* REST API integration
* MongoDB storage
* Vendor Portal integration
* Email notifications
* UiPath Orchestrator scheduling
* Dashboard reporting

---

*This project is intended for educational and portfolio purposes.*
