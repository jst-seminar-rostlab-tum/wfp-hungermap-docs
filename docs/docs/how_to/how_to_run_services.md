# How to Run All Services

Author: `Georgi Peev`

This guide explains how to set up and run the WFP Chatbot and Email services - particularly, all the parsers, uploaders, and services required to run the WFP Hungermap project.

---

## Environment Setup

1. Create and activate a Python virtual environment:
```bash
# Create venv
python -m venv venv

# Activate venv
# On Windows:
venv\Scripts\activate
# On Unix/MacOS:
source venv/bin/activate
```

2. Install required dependencies:
```bash
pip install -r requirements.txt
```

---

## Prerequisites

Before starting any service:

1. Ensure MongoDB is running and accessible

2. Copy environment templates and configure variables:

| Service | Template Location | Target Location | Required Variables                                                                                                             |
|---------|------------------|-----------------|--------------------------------------------------------------------------------------------------------------------------------|
| Chatbot | `.env_template` | `.env` | `MONGODB_URI`<br/>`MONGODB_DB`<br/>`MONGODB_COLLECTION`<br/>`OPENAI_API_KEY`                                                   |
| Email | `email_service/.env_template` | `email_service/.env` | `MONGODB_URI`<br/>`MONGODB_DB_EMAIL_SERVICE`<br/>`BREVO_API_KEY`<br/>`BREVO_SENDER_EMAIL`<br/>`BREVO_SENDER_NAME`<br/>`BASE_URL` |

---

## Running the Email Service

Start the service:
```bash
python src/email_service/app.py
```

Available endpoints at `http://localhost:5001`:

| Endpoint | Method | Purpose |
|----------|---------|----------|
| `/send-email` | POST | Send email to recipient |
| `/subscribe` | POST | Subscribe to topics |
| `/unsubscribe` | POST | Unsubscribe from topics |
| `/topics` | GET | List available topics |
| `/daily-reports/countries` | GET | Get countries with daily reports |

---

## Running the Chatbot

Start the service:
```bash
python src/app.py
```

Available endpoints at `http://localhost:5000`:

| Endpoint | Method | Purpose |
|----------|---------|----------|
| `/query` | POST | Main chatbot interaction endpoint |
| `/docs` | GET | OpenAPI documentation |
| `/redoc` | GET | Alternative API documentation |

See [API Documentation](../chatbot/api.md) for detailed endpoint specifications.

---

## Running Data Pipeline

### 1. Run All Parsers

Execute all data parsers:
```bash
python src/run_all_parsers.py
```

This will run parsers for:
- Country reports
- General country data
- Additional country data
- PDC (Pacific Disaster Center) data
- Conflict data
- IPC (Integrated Food Security Phase Classification) data
- ISO3 country codes
- Yearly reviews

### 2. Run All Uploaders

Upload all parsed data to MongoDB:
```bash
python src/run_all_uploaders.py
```

This will upload:
- Country data (general, additional, PDC, conflict, IPC)
- Population and economic data
- Food security data (FCS, RCSI)
- Reports (country reports, yearly reviews)

### 3. Run Report Processing

Process and upload report content:
```bash
python src/report_chatting/upload_all_reports.py
```

## Running Everything

To run all services in the correct order:

1. Start MongoDB (if not already running)
2. Run parsers and uploaders:
```bash
python src/run_all_parsers.py
python src/run_all_uploaders.py
python src/report_chatting/upload_all_reports.py
```

3. Start the services:
```bash
# In separate terminals:
python src/email_service/app.py
python src/app.py
```

### Common Issues

1. **MongoDB Connection**
   - Check if MongoDB is running
   - Verify connection string in `.env`
   - Check network connection

2. **Missing Data**
   - Run parsers individually to identify issues
   - Check if API endpoints are accessible
   - Verify file permissions in `src/assets/`

3. **Service Issues**
   - Check if MongoDB collections exist and have data
   - Verify that all required environment variables are set
   - Ensure ports 5000 and 5001 are available
   - Ensure all dependencies are installed
