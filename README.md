# MedStock
### National Medicine Availability & Referral System — Botswana

> A full-stack AI-powered web application that gives patients real-time visibility into medicine availability across all public health facilities in Botswana, and gives health administrators a live command centre to monitor stock, predict shortages, and coordinate redistribution.

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Features at a Glance](#2-features-at-a-glance)
3. [Project Structure](#3-project-structure)
4. [Data Sources (CSV Datasets)](#4-data-sources-csv-datasets)
5. [Installation & Setup](#5-installation--setup)
6. [Running the Application](#6-running-the-application)
7. [URL Routes Reference](#7-url-routes-reference)
8. [Patient Portal - Full Guide](#8-patient-portal--full-guide)
9. [Admin Dashboard - Full Guide](#9-admin-dashboard--full-guide)
10. [Admin Login Credentials](#10-admin-login-credentials)
11. [Language Support](#11-language-support)
12. [How the AI Engine Works](#12-how-the-ai-engine-works)
13. [Template Auto-Write System](#13-template-auto-write-system)
14. [Data Privacy & Design Decisions](#14-data-privacy--design-decisions)
15. [Known Limitations & Future Work](#15-known-limitations--future-work)


## 1. Project Overview

MedStock was built to solve a real public health problem in Botswana: patients travelling long distances to health facilities only to find that the medicine they need is out of stock. At the same time, health administrators have no unified view of national stock levels, expiry risks, or where to redistribute surplus supplies.

MedStock provides two distinct interfaces running from a single Flask application:

- **The Patient Portal** - a public-facing search tool (no login required) where any person can look up which clinics and hospitals near them currently stock a specific medicine, filterable by district.
- **The Admin Dashboard** - a secure, login-protected command centre for health officials to monitor inventory, act on AI-generated alerts, track expiry dates, manage redistribution, and analyse disease trends all from one tabbed interface.

The system is built on **9 real synthetic CSV datasets** representing 60 facilities, 10 medicines, and 9 districts across Botswana.

## 2. Features at a Glance

### Patient Portal
| Feature | Description |
| Medicine search | Type-ahead autocomplete search across all 10 medicines |
| Category filter | Filter results by disease category (Malaria, HIV, etc.) |
| District filter | Filter by any of 9 Botswana districts via dropdown or chip buttons |
| Availability status | Clear In Stock / Low Stock / Out indicators - no confusing unit counts shown to patients |
| Facility listing | Shows facility name, village, district, and facility type for each location carrying the medicine |
| District browse grid | One-click district cards at the bottom of the page to jump straight to district results |
| Bilingual UI | Full English and Setswana language toggle, persisted across sessions via localStorage |

### Admin Dashboard
| Feature | Description |
|---|---|
| Stat cards | Live counts for total stock, critical alerts, active facilities, predicted shortages |
| AI alert banner | Prominent red banner when unacknowledged alerts exist, with one-click jump to Alerts tab |
| 7 interactive tabs | Overview, Alerts, Predictions, Inventory, Batches, Disease Trends, Facilities |
| AI critical alerts | Colour-coded list of stockouts, expiry risks, and 7-day shortage predictions |
| Stockout predictions | AI-computed 7-day and 30-day stockout probabilities per facility-medicine pair |
| Redistribution orders | AI-recommended medicine transfers between districts with urgency levels |
| Inventory snapshot | Full table of all 600 inventory records with stock status and days-of-stock |
| Batch expiry tracker | All high-risk batches flagged by the AI with quantities expiring within 30 and 60 days |
| Disease risk scores | District-level risk scores for Malaria, HIV, and COVID with inline progress bars |
| Symptom reports | Latest weekly patient symptom counts per district (fever, headache, chills, vomiting) |
| Facility directory | Card grid of all 60 registered facilities with location and type |
| Live clock | Real-time clock in the page header |
| Bilingual UI | EN / TN language toggle matching the patient portal |


## 3. Project Structure

```
SentinelBW\/
│
├── app.py                          # Main Flask application (all logic + embedded templates)
│
├── templates/                      # Auto-generated on startup - do not edit manually
│   ├── admin_dashboard.html        # Admin dashboard (7 tabs)
│   ├── admin_login.html            # Login page
│   └── patients.html               # Public patient portal
│
├── static/
│   └── style.css                   # Global stylesheet
│
├── facilities.csv                  # 60 health facilities with lat/lng
├── medicines.csv                   # 10 medicine types
├── inventory_snapshot.csv          # 600 stock records (facility × medicine)
├── inventory_batches.csv           # 6,457 batch-level records with expiry dates
├── expiry_summary.csv              # Expiry risk aggregated per facility-medicine
├── stockout_risk.csv               # 7-day and 30-day stockout probabilities
├── redistribution_recommendations.csv  # 11 AI transfer orders
├── district_risk.csv               # Disease risk scores per district per week
├── disease_cases.csv               # Confirmed and suspected cases by district/week
├── symptom_reports.csv             # Patient symptom reports by district/week
├── weather_data.csv                # Rainfall, temperature, humidity per district
│
└── README.md                       # This file
```

> **Note:** The `templates/` folder and all HTML files inside it are automatically created every time `app.py` starts. They are embedded as base64 strings inside `app.py` and written to disk on startup. You never need to copy or manage template files manually.


## 4. Data Sources (CSV Datasets)

All datasets are synthetic but modelled on real Botswana geography and public health data. They must be placed in the same folder as `app.py` (or in a `data/` subfolder).

| File | Rows | Description |
|---|---|---|
| `facilities.csv` | 60 | Health facilities including name, type (Clinic/Hospital/Health Post), district, village, latitude, longitude, and cold storage availability |
| `medicines.csv` | 10 | Medicine records including name, disease category, and dosage form |
| `inventory_snapshot.csv` | 600 | One row per facility-medicine pair showing quantity on hand, reorder point, days of stock left, and stock status (Available/Low/Out) |
| `inventory_batches.csv` | 6,457 | Individual batch records with batch numbers, manufacture date, expiry date, and quantity |
| `expiry_summary.csv` | 594 | Aggregated expiry risk per facility-medicine with quantities expiring within 30 and 60 days |
| `stockout_risk.csv` | 1,201 | AI-predicted stockout probabilities at 7-day and 30-day horizons per facility-medicine |
| `redistribution_recommendations.csv` | 11 | AI-generated transfer recommendations between districts with urgency level and reason |
| `district_risk.csv` | 391 | Weekly disease risk scores (0-100) per district for Malaria, HIV, and COVID |
| `disease_cases.csv` | 391 | Weekly suspected and confirmed disease case counts per district |
| `symptom_reports.csv` | 131 | Weekly patient symptom reports per district (fever, headache, chills, vomiting counts) |
| `weather_data.csv` | 131 | Weekly weather data per district (rainfall, temperature, humidity) - loaded by app for future use |

### Districts covered
Central · Chobe · Ghanzi · Kgalagadi · Kgatleng · Kweneng · North-East · South-East · Southern

### Medicines tracked
Malaria Rapid Diagnostic Test (RDT) · ACT Adult Dose · ACT Pediatric Dose · ARV Regimen TLD · Cotrimoxazole Prophylaxis · COVID Antigen Test · Paracetamol · Oral Rehydration Salts (ORS) · PPE Kit · Oxygen Cylinder Refill Units


## 5. Installation & Setup

### Prerequisites

- Python 3.8 or higher
- pip

### Step 1 - Clone or download the project

Place all project files in a single folder, e.g. `C:\Users\yourname\PycharmProjects\SentinelBW\.`

### Step 2 - Install dependencies

```bash
pip install flask
```

Flask is the only dependency. No other packages are required; the app uses Python's built-in `csv`, `os`, and `base64` modules for everything else.

### Step 3: Place your CSV files

Copy all 11 CSV files into the **same folder as `app.py`**. The app will search both the root folder and a `data/` subfolder automatically:

```
SentinelBW/
├── app.py
├── facilities.csv
├── medicines.csv
├── inventory_snapshot.csv
├── expiry_summary.csv
├── stockout_risk.csv
├── redistribution_recommendations.csv
├── district_risk.csv
├── disease_cases.csv
├── symptom_reports.csv
├── weather_data.csv
└── inventory_batches.csv
```

If a CSV file is missing, the app will display a clear error message showing the exact path it was looking for.

### Step 4: Ensure the static folder exists

```
SentinelBW/
└── static/
    └── style.css
```

The `templates/` folder is created automatically — you do not need to create it.

---

## 6. Running the Application

### From the terminal/command prompt

```bash
cd path/to/SentinelBW
python app.py
```

### From PyCharm

Open `app.py` and click the green **Run** button, or press `Shift+F10`.

### What happens on startup

1. All 9 CSV files are loaded into memory
2. Lookup dictionaries are built for fast joins (medicines, facilities)
3. All AI metrics (alerts, risk scores, predictions) are pre-computed
4. All 3 HTML templates are written to the `templates/` folder from embedded base64
5. Flask starts on `http://127.0.0.1:5000`

### Open the app

```
http://127.0.0.1:5000
```

The root URL automatically redirects to the admin login page. To go directly to the patient portal:

```
http://127.0.0.1:5000/patient
```



## 7. URL Routes Reference

| URL | Method | Access | Description |
|---|---|---|---|
| `/` | GET | Public | Redirects to `/admin/login` |
| `/patient` | GET | Public | Patient portal home |
| `/patient/search` | GET | Public | Search results — accepts `?q=`, `?category=`, `?district=` |
| `/api/suggest` | GET | Public | Autocomplete JSON API — accepts `?q=`, returns up to 6 matches |
| `/admin/login` | GET, POST | Public | Admin login form |
| `/admin` | GET | Admin only | Full admin dashboard |
| `/admin/logout` | GET | Admin only | Clears session and redirects to login |

### Query parameters for `/patient/search.`

| Parameter | Example | Description |
|---|---|---|
| `q` | `?q=paracetamol` | Case-insensitive medicine name or category search |
| `category` | `?category=Malaria` | Filter by disease category |
| `district` | `?district=Chobe` | Filter results to one district only |

These parameters can be combined, e.g., `/patient/search?q=ACT&district=Chobe.`


## 8. Patient Portal — Full Guide

**URL:** `http://127.0.0.1:5000/patient`  
**Access:** No login required, fully public

### How to find a medicine

1. **Type in the search box** start typing a medicine name. A dropdown of suggestions appears automatically after the first character. Click any suggestion to search immediately.
2. **Use the Category dropdown** to narrow down by disease area (Malaria, HIV, General, etc.)
3. **Use the District dropdown** to filter to your local area.
4. Click **Search**.

### Reading search results

Each result card shows:
- **Medicine name** and dosage form
- **Availability status** one of three clearly colour-coded banners:
  - 🟢 **Available at multiple facilities** well stocked, go to any listed facility
  - 🟡 **Limited availability act soon** stock is low, visit soon
  - 🔴 **Currently out of stock** not available at this time
- **Facility list** every clinic or hospital that carries this medicine, showing the facility name, village, district, facility type, and whether stock is In Stock, Low Stock, or Out

> Stock unit numbers are intentionally hidden from patients to avoid confusion and prevent unnecessary crowding at specific facilities.

### District filter chips

Below the search bar, there is a row of district chips. Clicking any district immediately filters the view to only show facilities in that district. The **All** chip clears the filter.

### Browse by District grid

At the bottom of the page, a grid of all 9 district cards lets you jump straight to district-filtered results without typing a medicine name useful if you just want to see what is available near you.

### Language toggle

The top-right of the navigation bar has an **EN / TN** toggle. Switching to **TN** translates all interface text to Setswana. This setting is saved in your browser and remembered on your next visit.

---

## 9. Admin Dashboard — Full Guide

**URL:** `http://127.0.0.1:5000/admin`  
**Access:** Login required (see credentials below)

### Stat Cards

Four summary cards at the top of the page give an instant reading of system health:

| Card | What it counts |
|---|---|
| **Total Stock Items** | Sum of all `quantity_on_hand` values across all 600 inventory records |
| **Critical Alerts** | Count of stockouts + high-expiry-risk records |
| **Active Facilities** | Total registered facilities (60) |
| **Predicted Shortages** | Facility-medicine pairs with High stockout risk in the next 30 days |

### Alert Banner

If there are any unacknowledged alerts, a red banner appears below the stat cards showing the total count and a **View Alerts** button that jumps directly to the Alerts tab.

### Tab Navigation

The dashboard is organised into **7 tabs**. Click any tab to switch content, no page reload required.


#### Tab 1 Overview

A summary view of the system state. Contains:
- Three metric cards: total stock, redistribution orders pending, and high-risk districts
- **Medicine Overview table** one row per medicine showing how many facilities are Out, Low, or Available for that medicine across the country


#### Tab 2 — Alerts

The AI-generated alert feed. Three types of alerts are shown:

| Alert Type | Trigger | Colour |
|---|---|---|
| 🔴 CRITICAL | `stock_status - 'Out'` in inventory snapshot | Red left border |
| ⏳ EXPIRY | `expiry_risk_level - 'High'` in expiry summary | Amber left border |
| 📉 SHORTAGE | `stockout_risk_level - 'High'` at 7-day horizon | Purple left border |

Each alert shows the medicine name, the facility and district affected, and a specific action message.


#### Tab 3 - Predictions

Two tables:

**7-Day Stockout Risk** - All facility-medicine pairs flagged as High risk of running out within 7 days. Shows expected demand and exact stockout probability percentage.

**Redistribution Recommendations** - The 11 AI-generated transfer orders. Each row shows which medicine to move, from which district, to which district, how many units, the urgency level (High/Medium/Low), and the reason the AI flagged this transfer.


#### Tab 4 - Inventory

The full inventory snapshot table - all 600 records. Columns:
- Facility name and district
- Medicine name
- Quantity on hand
- Reorder point
- Days of stock left (colour-coded: red under 14 days, yellow under 30, green above 30)
- Stock status pill (Available / Low / Out)


#### Tab 5 - Batches

All batches flagged as **High expiry risk**. For each record:
- Facility and medicine
- Units expiring within 30 days (red pill)
- Units expiring within 60 days (yellow pill)
- Date of the earliest expiring batch

This tab is intended to prompt administrators to redistribute or use expiring stock before it is wasted.


#### Tab 6 - Disease Trends

Two tables:

**District Risk Scores** - Latest AI-computed risk scores (0–100) per district for Malaria, HIV, and COVID. Includes an inline visual progress bar and colour-coded risk level (High/Medium/Low) plus projected cases for next week.

**Patient Symptom Reports** - Latest weekly counts of fever, headache, chills, and vomiting by district. The total column is colour-coded to highlight districts with elevated symptom burden.


#### Tab 7 - Facilities

A card grid of all 60 registered health facilities in Botswana, showing:
- Facility name
- Village, district, and facility type
- Online status indicator


## 10. Admin Login Credentials

Three accounts are pre-configured in the application:

| Username | Password | Name | Role |
|---|---|---|---|
| `admin` | `admin123` | System Admin | Super Admin |
| `doctor` | `doctor456` | Dr. Kefilwe Moyo | Medical Officer |
| `pharmacist` | `pharma789` | Thabo Sithole | Pharmacist |

> ⚠️ These are development credentials. Before deploying to a production environment, replace the `ADMIN_USERS` dictionary in `app.py` with a proper hashed password authentication system and a database backend.

## 11. Language Support

MedStock supports two languages across both the patient portal and the admin dashboard:

| Code | Language | Coverage |
|---|---|---|
| `EN` | English | Full — default language |
| `TN` | Setswana | Full — all UI labels, headings, buttons, status messages, placeholders |

### How it works

- An **EN / TN toggle** sits in the top-right of the navigation bar on every page.
- Clicking a language button applies translations instantly — no page reload.
- The selected language is saved to **`localStorage`** under the key `sentinel_lang` and is automatically restored the next time the user opens the app.
- Translations are embedded directly in the HTML using `data-en` and `data-tn` attributes. A small JavaScript function `setLang(lang)` iterates over all elements carrying these attributes and swaps their inner content.
- The autocomplete suggestion dropdown also respects the current language, showing Setswana stock status labels when TN is active.



## 12. How the AI Engine Works

MedStock does not connect to an external AI service. All "AI" functionality is rule-based inference computed from the CSV data at startup. The term AI refers to the automated analytical layer that processes raw data and surfaces actionable intelligence.

### Alert generation logic

```
Critical alert  →  inventory_snapshot.stock_status == "Out"
Expiry alert    →  expiry_summary.expiry_risk_level == "High"
Shortage alert  →  stockout_risk.stockout_risk_level == "High"
                   AND stockout_risk.horizon_days == "7"
```

### Stockout risk

The `stockout_risk.csv` file contains pre-computed probabilities generated from historical transaction patterns, current stock levels, and expected demand. The app filters this to the **7-day horizon** for the Alerts tab and the **30-day horizon** for the predicted shortages stat card.

### District disease risk

For each district-disease combination, the app selects only the **most recent week's** data from `district_risk.csv` to avoid stale readings influencing the display.

### Redistribution recommendations

The `redistribution_recommendations.csv` contains AI-generated orders. Each row identifies a source district with surplus stock and a target district with a deficit, along with the recommended transfer quantity and urgency.



## 13. Template Auto-Write System

All three HTML templates (`admin_dashboard.html`, `admin_login.html`, `patients.html`) are **embedded directly inside `app.py`** as base64-encoded strings.

Every time the application starts, the templates are decoded and written to the `templates/` folder automatically. This means:

- You never need to manually copy, move, or manage HTML files
- The app is self-contained in a single Python file
- If a template file is accidentally deleted or corrupted, it is restored on the next restart
- The `templates/` directory is created if it does not exist

This design was chosen because the development environment (PyCharm on Windows) created empty placeholder files that caused `TemplateNotFound` errors. Embedding templates eliminates this class of error entirely.



## 14. Data Privacy & Design Decisions

### Why patients cannot see unit counts

The patient-facing interface deliberately does not show how many units of a medicine are in stock. This decision was made for two reasons:

1. **Prevent panic buying / crowding** — showing that a facility has only 3 units left could cause all patients to rush to the same location simultaneously, depleting stock faster and leaving others without medicine.
2. **Clarity** — patients only need to know *where* to go, not *how much* is there. A simple In Stock / Low / Out label is more useful for decision-making.

### Why the patient portal requires no login

Medicine availability is public health information. Requiring patients to create accounts would create a barrier that disproportionately affects people with limited digital literacy or no smartphone data. The portal is designed to work for anyone with a basic browser.

### Data snapshot date

All inventory data reflects a snapshot from **2026-02-21**. In a production system, this would be replaced by a live database connection updated by facility staff or automated reporting systems.



## 15. Known Limitations & Future Work

| Limitation | Suggested improvement |
|---|---|
| Data is static (CSV files loaded at startup) | Connect to a live database (PostgreSQL/SQLite) updated by facility staff |
| Admin passwords are plain text in code | Implement hashed passwords (bcrypt) with a user management database |
| No real-time updates | Add WebSocket support or periodic AJAX polling to refresh stats |
| No facility staff interface | Build a data entry portal where pharmacy staff can update stock counts |
| Language is client-side only | Move Setswana translations to a proper i18n framework (Flask-Babel) for server-side rendering |
| No SMS/notification system | Integrate Africa's Talking or a similar SMS gateway to push alerts to facility managers |
| Weather data loaded but unused | Use weather variables as features in an ML model to improve stockout forecasting |
| No audit log | Track which admin acknowledged which alert and when |
| Single-file template system | Move to a proper Jinja2 template hierarchy with base templates and blocks |



## Built With

- **[Flask](https://flask.palletsprojects.com/)** — Python web framework
- **[Jinja2](https://jinja.palletsprojects.com/)** — HTML templating (bundled with Flask)
- **[Inter](https://fonts.google.com/specimen/Inter)** — UI typeface (Google Fonts)
- **Python standard library** — `csv`, `os`, `base64`, `collections`, `json`
- **Vanilla JavaScript** — language toggle, tab switching, autocomplete, live clock

No JavaScript frameworks, no npm, no build step. The entire frontend runs on plain HTML, CSS, and vanilla JS.



## Quick Start Summary

```bash
# 1. Install Flask
pip install flask

# 2. Place all CSV files in the project folder alongside app.py

# 3. Run
python app.py

# 4. Open browser
# Patient portal  →  http://127.0.0.1:5000/patient
# Admin login     →  http://127.0.0.1:5000/admin/login
#   username: admin  |  password: admin123
```

---

*MedStock — Protecting Botswana's medicine supply chain, one facility at a time.*
