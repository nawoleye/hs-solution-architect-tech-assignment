# HubSpot Integration Backend - Breezy Technical Assessment

This is a backend server for the HubSpot Solutions Architect Technical Assessment. It provides a proxy layer between your frontend application and the HubSpot CRM API.

## Overview

This Express.js server handles authentication and proxies requests to the HubSpot API. You'll build a frontend application that calls these endpoints to demonstrate how Breezy (a smart home technology company) would integrate their platform with HubSpot.

## Prerequisites

- Node.js (v14 or higher)
- npm or yarn
- A free HubSpot account
- HubSpot Private App access token

## Setup Instructions

### 1. Install Dependencies

```bash
npm install
```

### 2. Get Your HubSpot Access Token

1. Sign up for a [free HubSpot account](https://offers.hubspot.com/free-trial)
2. Navigate to **Settings** → **Integrations** → **Private Apps**
3. Click **Create a private app**
4. Give it a name (e.g., "SA Assessment App")
5. Go to the **Scopes** tab and enable:
   - `crm.objects.contacts.read`
   - `crm.objects.contacts.write`
   - `crm.objects.deals.read`
   - `crm.objects.deals.write`
6. Click **Create app** and copy your access token

### 3. Configure Environment Variables

```bash
cp .env.example .env
```

Edit `.env` and add your HubSpot token:
```
HUBSPOT_ACCESS_TOKEN=pat-na1-your-token-here
```

### 4. Start the Server

```bash
npm start
```

Or for development with auto-reload:
```bash
npm run dev
```

You should see:
```
✅ Server running on http://localhost:3001
Test it: http://localhost:3001/health
```

### 5. Test the Server

Open your browser or use curl:
```bash
curl http://localhost:3001/health
```

Should return:
```json
{
  "status": "Server is running",
  "timestamp": "2025-11-10T..."
}
```

## API Endpoints

### Health Check
**GET** `/health`

Check if the server is running.

**Response:**
```json
{
  "status": "Server is running",
  "timestamp": "2025-11-10T12:00:00.000Z"
}
```

---

### Get Contacts
**GET** `/api/contacts`

Fetch all contacts from HubSpot (limited to 50).

**Response:**
```json
{
  "results": [
    {
      "id": "12345",
      "properties": {
        "firstname": "Alex",
        "lastname": "Rivera",
        "email": "alex@example.com",
        "phone": "555-0123",
        "address": "123 Main St"
      }
    }
  ]
}
```

---

### Create Contact
**POST** `/api/contacts`

Create a new contact in HubSpot.

**Request Body:**
```json
{
  "properties": {
    "firstname": "Alex",
    "lastname": "Rivera",
    "email": "alex@example.com",
    "phone": "555-0123",
    "address": "123 Main St"
  }
}
```

**Response:**
```json
{
  "id": "12345",
  "properties": {
    "firstname": "Alex",
    "lastname": "Rivera",
    "email": "alex@example.com",
    ...
  }
}
```

---

### Get All Deals
**GET** `/api/deals`

Fetch all deals from HubSpot (limited to 50).

**Response:**
```json
{
  "results": [
    {
      "id": "67890",
      "properties": {
        "dealname": "Breezy Premium - Annual",
        "amount": "99",
        "dealstage": "closedwon"
      }
    }
  ]
}
```

---

### Create Deal
**POST** `/api/deals`

Create a new deal in HubSpot and associate it with a contact.

**Request Body:**
```json
{
  "dealProperties": {
    "dealname": "Breezy Premium - Annual Subscription",
    "amount": "99",
    "dealstage": "closedwon"
  },
  "contactId": "12345"
}
```

**Response:**
```json
{
  "id": "67890",
  "properties": {
    "dealname": "Breezy Premium - Annual Subscription",
    "amount": "99",
    "dealstage": "closedwon"
  }
}
```

---

### Get Deals for Contact
**GET** `/api/contacts/:contactId/deals`

Get all deals associated with a specific contact.

**Example:**
```
GET /api/contacts/12345/deals
```

**Response:**
```json
{
  "results": [
    {
      "id": "67890",
      "properties": {
        "dealname": "Breezy Premium - Annual",
        "amount": "99",
        "dealstage": "closedwon"
      }
    }
  ]
}
```

## Testing with cURL

### Create a contact:
```bash
curl -X POST http://localhost:3001/api/contacts \
  -H "Content-Type: application/json" \
  -d '{
    "properties": {
      "firstname": "Test",
      "lastname": "Customer",
      "email": "test@breezy.com"
    }
  }'
```

### Get all contacts:
```bash
curl http://localhost:3001/api/contacts
```

### Create a deal:
```bash
curl -X POST http://localhost:3001/api/deals \
  -H "Content-Type: application/json" \
  -d '{
    "dealProperties": {
      "dealname": "Breezy Premium - Monthly",
      "amount": "9.99",
      "dealstage": "closedwon"
    },
    "contactId": "12345"
  }'
```

## Common Deal Stages

For the Breezy use case, you can use these standard HubSpot deal stages:
- `appointmentscheduled` - Trial started
- `qualifiedtobuy` - Active trial user
- `closedwon` - Converted to paid subscription
- `closedlost` - Trial ended without conversion

## Error Handling

All endpoints return errors in this format:
```json
{
  "error": "Human-readable error message",
  "details": "Technical details from HubSpot API"
}
```

Common errors:
- **401 Unauthorized**: Check your `HUBSPOT_ACCESS_TOKEN` in `.env`
- **403 Forbidden**: Your private app may not have the required scopes
- **404 Not Found**: Contact or deal ID doesn't exist
- **500 Internal Server Error**: Check console logs for details

## Project Structure

```
2026-SA-Tech-Assessment/
├── server.js           # Main Express server
├── package.json        # Dependencies and scripts
├── .env.example        # Example environment variables
├── .gitignore         # Git ignore rules
└── README.md          # This file
```

## Your Task

Build a frontend application that:
1. Displays contacts from `GET /api/contacts`
2. Creates contacts via `POST /api/contacts`
3. Creates deals via `POST /api/deals`
4. Shows deals for each contact via `GET /api/contacts/:contactId/deals`
5. Incorporates an AI feature using OpenAI or Anthropic API

You can build your frontend in the `/public` folder or in a separate directory.

## Support

If you encounter issues:
1. Check that your `.env` file has a valid `HUBSPOT_ACCESS_TOKEN`
2. Verify your HubSpot Private App has all required scopes
3. Check the console logs for detailed error messages
4. Test endpoints with curl to isolate frontend vs backend issues

## Notes

- This server runs on port 3001
- CORS is enabled for all origins (development only)
- The server validates the HubSpot token on startup
- All HubSpot API calls are proxied through this server for security

Good luck with your assessment!
