# Vendor Invoice Management Portal

A cloud-based Vendor Invoice Management Portal that enables vendors to submit multiple invoice records through a responsive web interface. The application stores structured invoice batches in MongoDB Atlas and sends a JSON representation of each submission through Gmail for downstream processing and future RPA integration.

> **Live Application:** [Vendor Invoice Portal Submission](https://vendor-invoice-client.vercel.app/)

---

## The Problem

Traditional invoice submission through email can create several operational challenges:

- Invoice information is manually prepared and submitted through email
- Invoice data can be inconsistent in structure and format
- Records are difficult to centralize and trace
- Manual data entry is required for downstream processing
- Automation systems need a structured data source before invoice processing can begin

## The Solution

This project provides a centralized web portal where vendors can submit multiple invoices in a single submission.

The portal collects structured invoice information, calculates invoice totals in the browser, sends the submission to a REST API, stores the invoice batch in MongoDB Atlas, and generates a structured JSON attachment through Gmail.

The JSON email output also provides a practical integration point for downstream RPA workflows such as UiPath.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| HTML5 | Web page structure |
| CSS3 | Custom styling |
| Bootstrap | Responsive UI components and layout |
| JavaScript | Frontend logic, calculations, and API communication |
| Node.js | Backend runtime |
| Express.js | REST API framework |
| Mongoose | MongoDB object modeling |
| MongoDB Atlas | Cloud database |
| Nodemailer | Email notification and JSON attachment delivery |
| Gmail | SMTP email service |
| CORS | Cross-origin frontend/backend communication |
| dotenv | Environment variable management |
| Vercel | Cloud deployment and serverless API hosting |
| Nodemon | Local development server |

The backend dependencies are defined in `Vendor_Invoice_Server/package.json`, including Express, Mongoose, CORS, dotenv, Nodemailer, serverless-http, and Nodemon. The client repository contains the static web application and its supporting Node/npm configuration. 

---

## Architecture

The application uses a **client-server architecture** with a static frontend deployed on Vercel and an Express.js backend exposed as a Vercel serverless function.

The backend is responsible for:

1. Receiving invoice submissions from the frontend
2. Persisting invoice batches in MongoDB Atlas
3. Generating structured JSON invoice data
4. Sending the JSON data through Gmail
5. Providing previously submitted invoices through a REST API

```text
                              Vendor
                                │
                                ▼
                 ┌──────────────────────────┐
                 │     Vercel Frontend    │
                 │                        │
                 │ HTML + CSS + Bootstrap │
                 │       JavaScript       │
                 └────────────┬─────────────┘
                              │
                              │ HTTPS
                              │
                     GET /api/invoices
                     POST /api/invoices
                              │
                              ▼
                 ┌──────────────────────────┐
                 │     Vercel Backend     │
                 │                        │
                 │      Express.js        │
                 │       REST API         │
                 └────────────┬─────────────┘
                             │
                    ┌─────────┴───────────┐
                    │                   │
                    ▼                   ▼
          ┌──────────────────┐   ┌────────────────────┐
          │   MongoDB Atlas │  │   Gmail / SMTP    │
          │                 │  │                   │
          │  InvoiceBatch   │  │ JSON Attachment   │
          │  Documents      │  │ Email Notification│
          └──────────────────┘   └─────────┬──────────┘
                                        │
                                        ▼
                                  Downstream Automation
                                      / UiPath
```

### Backend Request Architecture

```text
Client
  │
  ▼
Vercel
  │
  ▼
api/index.js
  │
  ▼
app.js
  │
  ├── CORS
  ├── JSON Body Parser
  ├── Database Middleware
  │
  ▼
/api/invoices
  │
  ▼
invoiceRoutes.js
  │
  ▼
invoiceController.js
  │
  ├── InvoiceBatch Model
  │        │
  │        ▼
  │   MongoDB Atlas
  │
  └── Nodemailer
           │
           ▼
      Gmail SMTP
```

The backend's `api/index.js` exports the Express application, while `vercel.json` routes requests to that serverless entry point. The API configuration sets a 30-second maximum function duration. 

---

## Frontend Architecture

The frontend is implemented as a static HTML/CSS/JavaScript application.

### Main Pages

| Page | Purpose |
|---|---|
| `index.html` | Landing/home page |
| `apply.html` | Vendor invoice submission form |
| `invoices.html` | Submitted invoice listing |
| `success.html` | Submission result page |

### Frontend Structure

```text
Vendor_Invoice_Client/
│
├── assets/
│
├── css/
│   └── style.css
│
├── js/
│   ├── invoice.js
│   └── invoices.js
│
├── index.html
├── apply.html
├── invoices.html
├── success.html
├── favicon.ico
├── package.json
└── vercel.json
```

The invoice submission logic is handled by `js/invoice.js`, while `js/invoices.js` retrieves invoice records and renders them into the invoice table.

---

## Backend Architecture

The backend follows a lightweight layered structure:

```text
Vendor_Invoice_Server/
│
├── api/
│   └── index.js
│
├── config/
│   └── database.js
│
├── controllers/
│   └── invoiceController.js
│
├── models/
│   ├── Invoice.js
│   └── InvoiceBatch.js
│
├── routes/
│   └── invoiceRoutes.js
│
├── app.js
├── package.json
└── vercel.json
```

### Layer Responsibilities

| Layer | Responsibility |
|---|---|
| `api/` | Vercel serverless entry point |
| `app.js` | Express application configuration and middleware |
| `routes/` | API endpoint definitions |
| `controllers/` | Invoice business logic |
| `models/` | MongoDB/Mongoose schemas |
| `config/` | Database connection management |
| `vercel.json` | Vercel routing and serverless configuration |

---

## Key Capabilities

| Capability | Description |
|---|---|
| Vendor Submission | Vendors can submit invoice information through the web portal |
| Multiple Invoice Submission | Multiple invoice items can be submitted as one invoice batch |
| Dynamic Invoice Rows | Users can add and remove invoice rows dynamically |
| Automatic Calculation | Invoice totals are calculated from quantity × unit price |
| Grand Total | The frontend calculates the combined value of all invoice rows |
| Payment Status | Supports `Belum Bayar` and `Lunas` |
| Centralized Storage | Invoice batches are persisted in MongoDB Atlas |
| Invoice Listing | Previously submitted invoices can be retrieved through the API |
| Email Notification | Invoice submissions trigger an email notification |
| JSON Export | Invoice data is attached to the email as JSON |
| Cloud Deployment | Frontend and backend are deployed on Vercel |
| RPA Integration Point | Structured JSON email data can be consumed by downstream automation |

---

## Invoice Submission Flow

The complete submission process works as follows:

```text
1. Vendor opens the Invoice Portal
             │
             ▼
2. Enters Vendor Name
             │
             ▼
3. Adds one or more invoices
             │
             ▼
4. Enters invoice details
             │
             ▼
5. Frontend calculates each invoice total
             │
             ▼
6. Frontend calculates Grand Total
             │
             ▼
7. Vendor clicks Submit All Invoices
             │
             ▼
8. JavaScript builds JSON request payload
             │
             ▼
9. POST /api/invoices
             │
             ▼
10. Express API receives request
             │
             ▼
11. InvoiceBatch saved to MongoDB Atlas
             │
             ▼
12. Backend generates JSON email payload
             │
             ▼
13. Nodemailer sends email through Gmail
             │
             ▼
14. JSON invoice attachment delivered
```

The frontend validates that the vendor name is not empty, collects each invoice row, calculates the total, builds the request payload, and sends it to the deployed API endpoint. 

---

## Invoice Calculation

The frontend calculates the total for each invoice using:

```text
Invoice Total = Quantity × Unit Price
```

The Grand Total is calculated by summing the totals of all invoice rows:

```text
Grand Total = Invoice 1 + Invoice 2 + ... + Invoice N
```

The calculation is performed dynamically whenever quantity or unit price changes.

Example:

```text
Quantity   = 2
Unit Price = Rp 1,500,000

Total = 2 × Rp 1,500,000
      = Rp 3,000,000
```

---

## Data Model

The backend uses a nested `InvoiceBatch` schema.

```text
InvoiceBatch
│
├── vendorName
│
├── invoices[]
│   │
│   ├── invoiceNo
│   ├── invoiceDate
│   ├── description
│   ├── qty
│   ├── unitPrice
│   ├── total
│   └── status
│
└── createdAt
```

### Invoice Batch

| Field | Type | Required | Description |
|---|---|---:|---|
| `vendorName` | String | Yes | Vendor submitting the invoice batch |
| `invoices` | Array | No | Collection of invoice items |
| `createdAt` | Date | No | Submission timestamp |

### Invoice Item

| Field | Type | Required | Description |
|---|---|---:|---|
| `invoiceNo` | String | Yes | Invoice number |
| `invoiceDate` | String | No | Invoice date |
| `description` | String | No | Invoice description |
| `qty` | Number | Yes | Invoice quantity |
| `unitPrice` | Number | Yes | Unit price |
| `total` | Number | Yes | Calculated invoice total |
| `status` | String | No | Payment status |

The current status values are restricted to:

- `Belum Bayar`
- `Lunas`

The schema also automatically stores the batch creation timestamp. 

---

## REST API

The backend currently exposes two invoice endpoints:

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/invoices` | Retrieve all invoice batches |
| POST | `/api/invoices` | Submit a new invoice batch |

The routes are registered under `/api/invoices` in the Express application. 

### Submit Invoice Batch

**Request**

```json
{
  "vendorName": "PT Example",
  "invoices": [
    {
      "invoiceNo": "INV-001",
      "invoiceDate": "2026-07-01",
      "description": "Software Development Service",
      "qty": 2,
      "unitPrice": 1500000,
      "total": 3000000,
      "status": "Belum Bayar"
    }
  ]
}
```

**Response**

```json
{
  "success": true,
  "message": "Invoices berhasil disimpan."
}
```

### Retrieve Invoices

```http
GET /api/invoices
```

The backend retrieves invoice batches from MongoDB and sorts them by `createdAt` in descending order, so newer submissions are returned first.

The frontend then iterates through each batch and its invoice items to populate the invoice table. 

---

## Database

The application uses **MongoDB Atlas** as its persistent data store.

The database connection is managed through `config/database.js`.

Because the backend is deployed on Vercel, the connection implementation caches the Mongoose connection at module level and reuses it while the serverless instance remains warm.

```text
API Request
    │
    ▼
connectDB()
    │
    ├── Existing connection?
    │       │
    │       └── Yes → Reuse connection
    │
    └── No → Create MongoDB connection
                    │
                    ▼
              MongoDB Atlas
```

The connection configuration includes:

- Connection caching
- 10-second server selection timeout
- 30-second socket timeout
- Maximum connection pool size of 10
- Disabled Mongoose command buffering

The MongoDB connection string is provided through the `MONGODB_URI` environment variable. 

---

## Email Integration

After an invoice batch is saved successfully, the backend generates an email using **Nodemailer** with Gmail as the transport service.

The email contains a JSON attachment representing the invoice submission.

### Email Processing

```text
InvoiceBatch
    │
    ▼
Calculate Grand Total
    │
    ▼
Build JSON Payload
    │
    ▼
Generate Unique Filename
    │
    ▼
Nodemailer
    │
    ▼
Gmail
    │
    ▼
JSON Attachment
```

The generated payload contains:

- Vendor name
- Submission timestamp
- Number of invoice items
- Grand total
- Invoice details

Example:

```json
{
  "vendorName": "PT Example",
  "submittedAt": "2026-07-01T10:30:00.000Z",
  "totalItems": 2,
  "grandTotal": 4500000,
  "invoices": [
    {
      "invoiceNo": "INV-001",
      "invoiceDate": "2026-07-01",
      "description": "Software Development Service",
      "qty": 2,
      "unitPrice": 1500000,
      "total": 3000000,
      "status": "Belum Bayar"
    }
  ]
}
```

The attachment filename follows this pattern:

```text
invoice_<vendor_name>_<timestamp>.json
```

An important design characteristic is that **database persistence and email delivery are treated separately**. The invoice batch is saved first. If the email fails, the backend logs the email error but still returns a successful invoice-save response. 

---

## CORS Configuration

The backend explicitly configures CORS for the deployed frontend and local development environment.

Allowed origins include:

```text
http://localhost:5500
https://vendor-invoice-client.vercel.app
```

The API allows:

- GET
- POST
- PUT
- DELETE
- OPTIONS

and accepts:

- `Content-Type`
- `Authorization`

This allows the browser-based frontend to communicate with the separate Vercel backend. 

---

## Deployment

The project is deployed as two independent Vercel applications.

### Frontend Deployment

```text
Vendor_Invoice_Client
        │
        ▼
Vercel
        │
        ▼
https://vendor-invoice-client.vercel.app
```

The frontend uses a simple Vercel configuration with clean URLs enabled.

### Backend Deployment

```text
Vendor_Invoice_Server
        │
        ▼
Vercel
        │
        ▼
https://vendor-invoice-server.vercel.app
        │
        ▼
Serverless Express API
```

The backend's Vercel configuration routes all incoming requests to:

```text
api/index.js
```

with a maximum function duration of 30 seconds.

---

## Environment Configuration

The backend requires environment variables for external services.

### MongoDB

```env
MONGODB_URI=<mongodb-atlas-connection-string>
```

### Gmail

```env
EMAIL_USER=<gmail-address>
EMAIL_PASS=<gmail-app-password>
EMAIL_RECEIVER=<recipient-email>
```

Never commit these values to source control.

---

## Setup

### Prerequisites

- Node.js
- npm
- MongoDB Atlas account
- Gmail account
- Git

### Backend

```bash
git clone https://github.com/CodeWithSalma/Vendor_Invoice_Server.git

cd Vendor_Invoice_Server

npm install
```

Create the required environment variables and start the development server:

```bash
npm run dev
```

The production start command is:

```bash
npm start
```

### Frontend

```bash
git clone https://github.com/CodeWithSalma/Vendor_Invoice_Client.git

cd Vendor_Invoice_Client

npm install
```

The repository is primarily a static HTML/CSS/JavaScript application. Its current npm configuration is primarily inherited from the project setup and should not be treated as the frontend runtime architecture.

---

## Running the Project

### Local Development

1. Clone the backend repository
2. Install backend dependencies
3. Configure MongoDB Atlas
4. Configure Gmail credentials
5. Set backend environment variables
6. Start the backend
7. Open the frontend locally
8. Enter a vendor name
9. Add one or more invoice records
10. Verify calculated totals
11. Submit the invoices
12. Verify the MongoDB record
13. Verify the email notification
14. Open the invoice listing page and verify the submitted data

### Production

The current deployed frontend communicates directly with:

```text
https://vendor-invoice-server.vercel.app/api/invoices
```

The invoice listing page uses the same API to retrieve stored invoice batches.

---

## Project Structure

### Client

```text
Vendor_Invoice_Client/
│
├── assets/
├── css/
│   └── style.css
├── js/
│   ├── invoice.js
│   └── invoices.js
│
├── index.html
├── apply.html
├── invoices.html
├── success.html
├── favicon.ico
├── package.json
└── vercel.json
```

### Server

```text
Vendor_Invoice_Server/
│
├── api/
│   └── index.js
├── config/
│   └── database.js
├── controllers/
│   └── invoiceController.js
├── models/
│   ├── Invoice.js
│   └── InvoiceBatch.js
├── routes/
│   └── invoiceRoutes.js
│
├── app.js
├── package.json
└── vercel.json
```

---

## Error Handling

The backend includes several levels of error handling:

### Database Connection Error

If MongoDB cannot be connected, the database middleware returns:

```json
{
  "error": "Database connection failed"
}
```

### Unknown Route

Requests to undefined routes return HTTP 404 with the requested method and path.

### Global Error Handler

Unhandled Express errors are caught by the global error handler and returned as JSON.

### Email Failure

Email delivery is intentionally treated as non-critical after database persistence. An email failure is logged without causing the invoice submission itself to fail.

---

## UiPath Integration

The application was designed as a potential **invoice intake layer** for a larger RPA workflow.

The current integration point is the JSON attachment generated by the backend.

```text
Vendor
   │
   ▼
Vendor Invoice Portal
   │
   ▼
REST API
   │
   ├──────────────► MongoDB Atlas
   │
   └──────────────► Gmail
                         │
                         ▼
                    JSON Attachment
                         │
                         ▼
                       UiPath
                         │
                         ├── Read JSON
                         ├── Validate Invoice
                         ├── Process Invoice
                         └── Generate Report
```

This allows the web application and RPA automation to remain loosely coupled:

- The portal handles invoice intake
- The API handles persistence
- Gmail provides a simple asynchronous handoff
- UiPath can consume the structured JSON
- The downstream automation can perform validation, processing, and reporting

This architecture also aligns naturally with the separate **UiPath Invoice Processing Automation** project.

---

## Business Impact

- Eliminates manual invoice submission through email
- Centralizes vendor invoice records
- Reduces repetitive data entry
- Standardizes invoice information
- Improves invoice traceability
- Provides structured invoice data for downstream automation
- Enables cloud-based deployment
- Creates a foundation for RPA integration
- Demonstrates full-stack development across frontend, backend, database, email integration, and cloud deployment

---

## Future Enhancements

- Vendor authentication and authorization
- Role-based access control
- Invoice editing and deletion
- Invoice search and filtering
- Pagination for larger datasets
- Invoice attachment upload
- PDF invoice generation
- Invoice approval workflow
- Invoice validation rules
- REST API authentication
- Audit logging
- UiPath integration
- Automated invoice processing
- AI-powered invoice extraction
- Dashboard and analytics
- Vendor email confirmation
- Advanced invoice status workflow

---

## Repositories

- **Frontend:** [Client Repository](https://github.com/CodeWithSalma/Vendor_Invoice_Client)
- **Backend:** [Server Repository](https://github.com/CodeWithSalma/Vendor_Invoice_Server)
- **Live Application:** [Vendor Invoice Porttal](https://vendor-invoice-client.vercel.app/)

---

*This project is intended for educational and portfolio purposes.*
