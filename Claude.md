# HubSpot Integration Backend Server

Build a Node.js/Express backend server that acts as a proxy between a frontend application and the HubSpot CRM API. This server will be provided to interview candidates as a starter template for the Breezy smart home technology integration case study.

## Project Structure

```
hubspot-sa-assessment/
├── server.js
├── package.json
├── .env.example
├── .gitignore
└── README.md
```

## Technical Requirements

### Core Technology

- Node.js with Express.js framework
- Environment variable management with `dotenv`
- HTTP client: `axios` for HubSpot API calls
- CORS enabled for frontend access

### Package Dependencies

```json
{
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "axios": "^1.6.0",
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

## API Endpoints to Implement

### 1. Health Check

**Endpoint:** `GET /health`

- Simple status check to verify server is running
- Returns: `{ status: 'Server is running', timestamp: ISO date }`

### 2. Get Contacts from HubSpot

**Endpoint:** `GET /api/contacts`

- Fetches contacts from HubSpot CRM API
- HubSpot API: `GET https://api.hubapi.com/crm/v3/objects/contacts`
- Query parameters: `limit=50`, `properties=firstname,lastname,email,jobtitle,company`
- Returns: Full HubSpot response (matches their API format)
- Error handling: Catch and return appropriate error details

### 3. Create Contact in HubSpot

**Endpoint:** `POST /api/contacts`

- Creates a new contact in HubSpot CRM
- HubSpot API: `POST https://api.hubapi.com/crm/v3/objects/contacts`
- Expected request body:

```json
{
  "properties": {
    "firstname": "string",
    "lastname": "string",
    "email": "string",
    "jobtitle": "string",
    "company": "string"
  }
}
```

- Returns: HubSpot's contact creation response
- Error handling: Return detailed error messages

### 4. Create Deal in HubSpot

**Endpoint:** `POST /api/deals`

- Creates a deal and associates it with a contact
- HubSpot API: `POST https://api.hubapi.com/crm/v3/objects/deals`
- Expected request body:

```json
{
  "dealProperties": {
    "dealname": "string",
    "amount": "string",
    "dealstage": "string"
  },
  "contactId": "string"
}
```

- Must include associations array to link deal to contact
- Association type: `associationCategory: "HUBSPOT_DEFINED"`, `associationTypeId: 3` (Deal to Contact)
- Returns: Created deal response
- Error handling: Provide clear error messages

### 5. Get Deals for Contact

**Endpoint:** `GET /api/contacts/:contactId/deals`

- Fetches all deals associated with a specific contact
- Two-step process:
  1. Get deal IDs from associations: `GET https://api.hubapi.com/crm/v3/objects/contacts/{contactId}/associations/deals`
  2. Batch read deal details: `POST https://api.hubapi.com/crm/v3/objects/deals/batch/read`
- Deal properties to fetch: `dealname, amount, dealstage, closedate, pipeline`
- Returns: `{ results: [deal objects] }` or `{ results: [] }` if no deals
- Error handling: Handle case where contact has no deals

### 6. Get All Deals

**Endpoint:** `GET /api/deals`

- Fetches all deals from HubSpot
- HubSpot API: `GET https://api.hubapi.com/crm/v3/objects/deals`
- Query parameters: `limit=50`, `properties=dealname,amount,dealstage,closedate,pipeline`
- Returns: Full HubSpot response
- Error handling: Catch and return errors

## Configuration Requirements

### Environment Variables

Create `.env.example` with:

```
# HubSpot Private App Access Token
# Get this from: Settings > Integrations > Private Apps
# Required scopes: crm.objects.contacts.read, crm.objects.contacts.write,
#                  crm.objects.deals.read, crm.objects.deals.write
HUBSPOT_ACCESS_TOKEN=your_token_here

# Optional: AI API keys for candidate use
# OPENAI_API_KEY=your_openai_key
# ANTHROPIC_API_KEY=your_anthropic_key
```

### Server Configuration

- Port: `3001`
- CORS: Enable for all origins (this is a development server)
- Body parser: JSON middleware enabled
- Startup validation: Exit if `HUBSPOT_ACCESS_TOKEN` not found

### .gitignore

```
node_modules/
.env
.DS_Store
```

## HubSpot API Authentication

All requests to HubSpot API must include:

```javascript
headers: {
  'Authorization': `Bearer ${HUBSPOT_ACCESS_TOKEN}`,
  'Content-Type': 'application/json'
}
```

## Error Handling Requirements

For all endpoints:

1. Use try-catch blocks
2. Log errors to console with context
3. Return appropriate HTTP status codes
4. Include error details in response:

```json
{
  "error": "Human-readable error message",
  "details": "Technical details from HubSpot API"
}
```

## NPM Scripts

Include in `package.json`:

```json
"scripts": {
  "start": "node server.js",
  "dev": "nodemon server.js"
}
```

## Startup Behavior

When server starts:

1. Load environment variables
2. Validate `HUBSPOT_ACCESS_TOKEN` exists (exit with error if missing)
3. Start Express server on port 3001
4. Log: "✅ Server running on http://localhost:3001"
5. Log: "Test it: http://localhost:3001/health"

## Code Quality Requirements

- Clear variable names
- Comments explaining HubSpot API interactions
- Consistent error handling pattern across all endpoints
- Async/await syntax (not .then/.catch)
- Proper HTTP status codes

## README.md Content

Create a README that includes:

1. Project description ("HubSpot Integration Backend for Breezy Technical Assessment")
2. Setup instructions
3. How to get a HubSpot access token
4. Environment variable configuration
5. How to start the server
6. Available endpoints documentation
7. Example requests for testing (can reference Breezy customer scenarios)

## Testing Recommendations

Suggest testing with:

- cURL commands for each endpoint
- Postman collection examples
- What to verify in HubSpot portal after API calls

## Notes for Implementation

- This is a starter template for candidates, so code should be clear and educational
- Include helpful console.log statements for debugging
- Error messages should be informative for learning purposes
- Don't over-engineer - keep it simple and functional
- Focus on demonstrating REST API patterns clearly
