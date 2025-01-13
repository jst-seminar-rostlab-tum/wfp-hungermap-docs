# How to Run All Services

## Prerequisites

Before starting any service, ensure:
1. MongoDB is running and accessible
2. You have copied `.env_template` to `.env` and set required variables:
   1. Copy root `.env_template` to `.env` for the Chatbot service:
   ```bash
   MONGODB_URI=your_mongodb_uri 
   MONGODB_DB=db_main 
   MONGODB_COLLECTION=chatbot_data 
   OPENAI_API_KEY=your_openai_api_key
   ```
   2. Copy `email_service/.env_template` to `email_service/.env` for the Email service:
   ```bash
   MONGODB_URI=your_mongodb_uri 
   MONGODB_DB_EMAIL_SERVICE=email_service 
   BREVO_API_KEY=your_brevo_api_key 
   BREVO_SENDER_EMAIL=jstwfp@gmail.com 
   BREVO_SENDER_NAME=WFP 
   BASE_URL=your_base_url
   ```
3. All dependencies are installed:
```bash
pip install -r requirements.txt
```

## Running the Email Service

1. Start the Email Service:
```bash
python src/email_service/app.py
```

The service will be available at `http://localhost:5001` with the following endpoints:
- `/send-email`: Send email to recipient
- `/subscribe`: Subscribe to topics
- `/unsubscribe`: Unsubscribe from topics
- `/topics`: List available topics
- `/daily-reports/countries`: Get countries with daily reports

## Running the Chatbot

1. Start the Chatbot service:
```bash
python src/app.py
```

The chatbot will be available at `http://localhost:5000` with these endpoints:
- `/query`: Main endpoint for chatbot interactions
   - Accepts POST requests with:
      - `reports_country_name` (optional): Country for report chatting
      - `query`: User's question
      - `version`: API version
      - `chatbot_type`: Model type (e.g., "gpt-4")
      - `limit`: Result limit
      - `previous_messages`: Chat history

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
