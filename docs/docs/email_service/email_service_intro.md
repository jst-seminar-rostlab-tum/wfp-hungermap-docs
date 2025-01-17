---
id: email-service
title: Email Service
slug: /email-service
sidebar_position: 1
---

# Email Service

The Email Service handles email communications - it uses Brevo as the email delivery provider and provides a REST API for email management. The service runs on port 5001 and includes subscription management capabilities.

## Purpose

The service is designed to:
- Send formatted email notifications using customizable templates
- Manage email subscriptions and topics
- Generate and distribute daily country reports
- Maintain consistent branding across all email communications
- Track subscriber preferences and unsubscribe requests

## Key Features

- **REST API Endpoints**:
  - `/send-email`: Send emails to recipients
  - `/subscribe`: Manage topic subscriptions
  - `/unsubscribe`: Handle unsubscribe requests
  - `/topics`: List available topics
  - `/daily-reports/countries`: Retrieve countries with daily reports

- **Email Templates**: 
  - HTML templates with responsive design
  - Separate CSS styling for maintainable designs
  - Dynamic content injection via template variables

- **Integration**:
  - Brevo API for reliable email delivery
  - MongoDB for subscription and configuration storage
  - Configurable sender identity (name and email)
  - Base URL configuration for links
