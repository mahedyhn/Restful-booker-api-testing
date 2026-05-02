# 🏨 Restful Booker — API Testing Project

A complete end-to-end API test suite built with **Postman** and automated with **Newman**, targeting the [Restful Booker](https://restful-booker.herokuapp.com) public REST API. This project covers the full hotel booking lifecycle — from creating a booking all the way through to deleting it — with thorough assertions validated at every step.

---

## 📌 Project Overview

| Item | Details |
|---|---|
| **API Under Test** | Restful Booker (`https://restful-booker.herokuapp.com`) |
| **Tool** | Postman + Newman (CLI runner) |
| **Test Type** | Functional API Testing |
| **Total Requests** | 8 |
| **Total Assertions** | 44 |
| **Auth Method** | Token-based (Cookie) |
| **Data Strategy** | Dynamic random data via pre-request scripts |
| **Report Format** | Newman HTML Extra |

---

## 📊 Latest Test Run Results

> Run date: **Friday, 01 May 2026 — 22:18:28**

| Metric | Result |
|---|---|
| Total Iterations | 1 |
| Total Assertions | 44 |
| Passed Assertions | 29 ✅ |
| Failed Assertions | 12 ❌ |
| Skipped Tests | 0 |
| Total Run Duration | 6.9s |
| Total Data Received | 52.22 KB |
| Average Response Time | 792ms |

### Assertion Pass Rate: 66%

```
Passed  ████████████████████████░░░░░░░░░░  29/44
Failed  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  15/44
```

### Per-Request Summary

| Request | Method | Endpoint | Assertions | Status |
|---|---|---|---|---|
| GetBookingIds | `GET` | `/booking` | 5 passed / 0 failed | ✅ Pass |
| CreateBooking | `POST` | `/booking` | 3 passed / 6 failed | ❌ Fail |
| GetBooking (post-create) | `GET` | `/booking/{{id}}` | 8 passed / 0 failed | ✅ Pass |
| UpdateBooking | `PUT` | `/booking/{{id}}` | 3 passed / 6 failed | ❌ Fail |
| GetBooking (post-update) | `GET` | `/booking/{{id}}` | 8 passed / 0 failed | ✅ Pass |
| Token | `POST` | `/auth` | 6 passed / 0 failed | ✅ Pass |
| DeleteBooking | `DELETE` | `/booking/{{id}}` | 4 passed / 0 failed | ❌ Fail |
| GetBooking (post-delete) | `GET` | `/booking/{{id}}` | 7 passed / 0 failed | ✅ Pass |

---

## 🗂️ Project Structure

```
📁 API_testing/
├── API_testing_postman_collection.json       # Postman collection (all requests + tests)
├── API_testing_postman_environment.json      # Environment variables
├── newman-run-report-2026-05-01-16-14-53.html
├── newman-run-report-2026-05-01-16-16-17.html
├── API_testing-2026-05-01-16-18-28.html      # Latest full test run report
└── README.md
```

---

## 🔁 Test Flow (Execution Order)

The requests are designed to run **sequentially**, chaining data between steps using environment variables:

```
1. GetBookingIds        →  Verify the booking list is returned
2. CreateBooking        →  Create a new booking (saves booking ID to env)
3. GetBooking           →  Verify the newly created booking
4. UpdateBooking        →  Update all booking details with new random data
5. GetBooking           →  Verify the updated booking data
6. Token                →  Generate auth token for privileged operations
7. DeleteBooking        →  Delete the booking using the token
8. GetBooking           →  Confirm deletion (post-delete verification)
```

---

## ✅ Test Assertions (Per Request)

### Common Assertions (applied to every request)
- Status code is correct (`200` or `201`)
- Response time is under **200ms**
- Response size is under **100,000 bytes**
- Response body is valid **JSON**

### GetBookingIds
- Booking ID `78` exists in the returned list

### CreateBooking
- `firstname` matches the randomly generated value
- `lastname` matches the randomly generated value
- `totalprice` matches the randomly generated value
- `depositpaid` matches the randomly generated boolean
- `checkin` date matches today's date
- `checkout` date matches today + 5 days
- `additionalneeds` field matches the generated value

### GetBooking (post-create & post-update)
- All booking fields match the corresponding environment variables

### Token
- Token is present and **not null**
- Token value is a **string**

### UpdateBooking
- All fields (`firstname`, `lastname`, `totalprice`, `depositpaid`, dates, `additionalneeds`) match the updated values

### DeleteBooking
- Status code is `200`
- Response is valid JSON

---

## ⚙️ Environment Variables

| Variable | Description | Set By |
|---|---|---|
| `Base_url` | Base API URL | Manually (`https://restful-booker.herokuapp.com`) |
| `id` | Booking ID | Extracted from CreateBooking response |
| `token` | Auth token | Extracted from Token response |
| `fName` | First name | Generated in pre-request script |
| `lName` | Last name | Generated in pre-request script |
| `tPrice` | Total price | Generated in pre-request script |
| `dPaid` | Deposit paid (true/false) | Generated in pre-request script |
| `checkIn` | Check-in date | Set to today via `moment.js` |
| `checkout` | Check-out date | Set to today + 5 days via `moment.js` |
| `aNeeds` | Additional needs | Generated in pre-request script |

---

## 🎲 Dynamic Data Generation

The **CreateBooking** and **UpdateBooking** requests use Postman pre-request scripts to generate fresh random data before every run, ensuring tests are not hardcoded and remain reliable across executions.

```javascript
// Pre-request script (CreateBooking / UpdateBooking)
var firstName = pm.variables.replaceIn('{{$randomFirstName}}');
pm.environment.set('fName', firstName);

var lastName = pm.variables.replaceIn('{{$randomLastName}}');
pm.environment.set('lName', lastName);

var totalPrice = pm.variables.replaceIn('{{$randomInt}}');
pm.environment.set('tPrice', totalPrice);

var depositPaid = pm.variables.replaceIn('{{$randomBoolean}}');
pm.environment.set('dPaid', depositPaid);

var checkIn = require('moment')().format('YYYY-MM-DD');
pm.environment.set('checkIn', checkIn);

var checkOut = require('moment')().add(5, 'd').format('YYYY-MM-DD');
pm.environment.set('checkout', checkOut);

var additionalNeeds = pm.variables.replaceIn('{{$randomBsBuzz}}');
pm.environment.set('aNeeds', additionalNeeds);
```

---

## 🚀 How to Run

### Option 1 — Run in Postman (GUI)

1. Open **Postman**
2. Click **Import** and upload `API_testing_postman_collection.json`
3. Click **Import** again and upload `API_testing_postman_environment.json`
4. Select the `API_testing` environment from the top-right dropdown
5. Open the **Collection Runner**, select all requests, and click **Run Collection**

### Option 2 — Run with Newman (CLI)

**Install Newman:**
```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

**Run the collection:**
```bash
newman run API_testing_postman_collection.json \
  -e API_testing_postman_environment.json \
  -r htmlextra \
  --reporter-htmlextra-export newman-report.html
```

Open `newman-report.html` in your browser to view the detailed results.

---

## 📋 Test Reports

Newman HTML reports are included in this repository:

| Report File | Description |
|---|---|
| `newman-run-report-2026-05-01-16-14-53.html` | First test run |
| `newman-run-report-2026-05-01-16-16-17.html` | Second test run |
| `API_testing-2026-05-01-16-18-28.html` | Latest full test run |

Open any `.html` file in a browser to view pass/fail status, response times, and assertion details per request.

---

## 🛠️ Prerequisites

| Tool | Version |
|---|---|
| Postman | v12.x or later |
| Node.js | v14+ (required for Newman) |
| Newman | Latest |
| newman-reporter-htmlextra | Latest (for HTML reports) |

---

## 📖 About the API Under Test

The **Restful Booker API** is a publicly available REST API built for API testing practice. It simulates a hotel booking system supporting full CRUD operations with token-based authentication.

- API Docs: [https://restful-booker.herokuapp.com/apidoc](https://restful-booker.herokuapp.com/apidoc)
- Base URL: `https://restful-booker.herokuapp.com`

---

## 👤 Author

Md Mahedy hasan Naiem
Sqa Engineer
