# Setup Guide: Shopify AI Customer Service Automation

This guide walks you through setting up the production-ready n8n workflow for automating customer service using OpenAI.

---

## Prerequisites

Before you begin, ensure you have:

1. **n8n instance** (Cloud or self-hosted)
2. **OpenAI API account** with API key
3. **Google account** for Google Sheets logging
4. **Shopify store** or any website with a contact form

---

## Step 1: Import the Workflow

1. Open your n8n instance
2. Click **"Workflows"** in the left sidebar
3. Click **"Add workflow"** → **"Import from File"**
4. Select `shopify-ai-workflow.json`
5. The workflow will appear in your canvas

---

## Step 2: Set Up OpenAI Credentials

### Create OpenAI API Key

1. Go to [OpenAI Platform](https://platform.openai.com/)
2. Sign in or create an account
3. Navigate to **API Keys** section
4. Click **"Create new secret key"**
5. Copy the API key (you won't see it again!)

### Add Credentials to n8n

1. In n8n, click **"Credentials"** in the left sidebar
2. Click **"Add Credential"**
3. Search for **"OpenAI"**
4. Select **"OpenAI API"**
5. Paste your API key
6. Click **"Save"**
7. Note the credential name (default: "OpenAI API")

### Link Credential to Workflow

1. Open the workflow
2. Click on the **"OpenAI Customer Service"** node
3. In the **"Credential to connect with"** dropdown, select your OpenAI credential
4. Click **"Save"**

---

## Step 3: Set Up Google Sheets

### Create the Spreadsheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet
3. Name it **"Customer Service Log"** (or your preferred name)
4. In **Row 1**, add these column headers (exact names):

| Timestamp | Customer Name | Customer Email | Customer Message | AI Response | Confidence Score | Automation Status | Order Number | Processed At |
|-----------|---------------|----------------|------------------|-------------|------------------|-------------------|--------------|--------------|

5. Leave Row 2 and below empty (data will be appended here)
6. Copy the spreadsheet URL from your browser

### Add Google Sheets Credentials to n8n

1. In n8n, click **"Credentials"** → **"Add Credential"**
2. Search for **"Google Sheets"**
3. Select **"Google Sheets OAuth2 API"**
4. Click **"Sign in with Google"**
5. Authorize n8n to access your Google Sheets
6. Click **"Save"**

### Configure the Google Sheets Node

1. In the workflow, click on **"Log to Google Sheets"** node
2. In **"Credential to connect with"**, select your Google Sheets credential
3. In **"Document"** field:
   - Change mode to **"By URL"**
   - Paste your Google Sheets URL
4. In **"Sheet"** field:
   - Ensure mode is **"By Name"**
   - Verify the sheet name matches your sheet tab (default: "Customer Service Log")
5. Click **"Save"**

---

## Step 4: Configure the Webhook

### Get Your Webhook URL

1. Click on the **"Customer Service Webhook"** node
2. At the top of the node panel, you'll see two URLs:
   - **Test URL**: For testing during development
   - **Production URL**: For live use after activation

3. Copy the **Production URL** (it will look like):
   ```
   https://your-n8n-instance.com/webhook/shopify-customer-service
   ```

### Test the Webhook (Optional but Recommended)

1. Click **"Listen for Test Event"** button on the webhook node
2. Use a tool like Postman, cURL, or your contact form to send a POST request:

**Example cURL command:**
```bash
curl -X POST https://your-n8n-instance.com/webhook-test/shopify-customer-service \
  -H "Content-Type: application/json" \
  -d '{
    "customerName": "Jane Doe",
    "customerEmail": "jane@example.com",
    "customerMessage": "What is your return policy for opened skincare products?",
    "orderNumber": "12345",
    "timestamp": "2025-01-15T10:30:00Z"
  }'
```

3. You should see the workflow execute and receive a response
4. Check your Google Sheet - a new row should appear

---

## Step 5: Customize Store Context (Optional)

The workflow includes default store information for a beauty/skincare store called "Glow Beauty". Customize this for your store:

1. Click on **"Process Customer Data"** node
2. Find the `storeContext` object in the code
3. Update these values:
   - `storeName`: Your store name
   - `storeType`: Type of business
   - `returnPolicy`: Your return policy
   - `shippingInfo`: Your shipping details
   - `productCategories`: Your product types
   - `support`: Your support contact info

**Example:**
```javascript
const storeContext = {
  storeName: 'Your Store Name',
  storeType: 'Your Business Type',
  returnPolicy: 'Your return policy here...',
  shippingInfo: 'Your shipping info here...',
  productCategories: ['Category1', 'Category2', 'Category3'],
  support: 'support@yourstore.com or 1-800-XXX-XXXX'
};
```

4. Click **"Save"**

---

## Step 6: Activate the Workflow

1. Click the **toggle switch** at the top right to activate the workflow
2. The webhook is now live and ready to receive customer service requests!

---

## Step 7: Integrate with Your Website

### Option A: Shopify Contact Form

Add this JavaScript to your Shopify contact form submission:

```javascript
// After form validation
const formData = {
  customerName: document.getElementById('name').value,
  customerEmail: document.getElementById('email').value,
  customerMessage: document.getElementById('message').value,
  orderNumber: document.getElementById('order').value || null,
  timestamp: new Date().toISOString()
};

fetch('https://your-n8n-instance.com/webhook/shopify-customer-service', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(formData)
})
.then(response => response.json())
.then(data => {
  if (data.automated) {
    // Show AI response to customer
    displayResponse(data.message);
  } else {
    // Show pending message
    displayResponse(data.message);
  }
})
.catch(error => {
  console.error('Error:', error);
  displayResponse('Thank you for your message. We will get back to you soon.');
});
```

### Option B: Zapier/Make Integration

1. Create a Zap/Scenario that triggers on new contact form submissions
2. Add an HTTP POST action
3. Configure it to send data to your n8n webhook URL
4. Map the form fields to the expected JSON structure

### Option C: Email to Webhook

Use a service like Zapier Email Parser or Mailparser to:
1. Forward customer service emails to a parsing service
2. Extract customer name, email, and message
3. POST the parsed data to your n8n webhook

---

## Step 8: Test End-to-End

### Test Case 1: High Confidence Question (Should Automate)

**Send this request:**
```json
{
  "customerName": "Sarah Johnson",
  "customerEmail": "sarah@example.com",
  "customerMessage": "What is your return policy?",
  "timestamp": "2025-01-15T14:30:00Z"
}
```

**Expected Result:**
- Receive automated AI response
- Response field `automated: true`
- Response field `status: "success"`
- New row appears in Google Sheets with `AUTOMATED` status

### Test Case 2: Low Confidence Question (Should Flag for Review)

**Send this request:**
```json
{
  "customerName": "Mike Chen",
  "customerEmail": "mike@example.com",
  "customerMessage": "Can you tell me the exact concentration of hyaluronic acid in your premium serum?",
  "timestamp": "2025-01-15T14:35:00Z"
}
```

**Expected Result:**
- Receive pending review message
- Response field `automated: false`
- Response field `status: "pending_review"`
- Response includes `aiSuggestion` for human agent
- New row in Google Sheets with `FLAGGED_FOR_REVIEW` status

---

## Monitoring & Maintenance

### Check Google Sheets Daily

- Review flagged interactions (Automation Status = "FLAGGED_FOR_REVIEW")
- Monitor confidence scores to tune the threshold if needed
- Look for patterns in low-confidence questions

### Adjust Confidence Threshold

If you want to change when questions get flagged:

1. Open the **"Check Confidence"** IF node
2. Change the `0.8` value to your preferred threshold:
   - **0.9**: Only very confident responses are automated (more conservative)
   - **0.7**: More responses automated (more aggressive)
3. Save and test

### Update AI Instructions

Improve responses by updating the system message in the **"OpenAI Customer Service"** node:

1. Click on the node
2. Find the **System** role message
3. Add more specific instructions or examples
4. Update store policies as they change

---

## Cost Estimates

### OpenAI Costs (using gpt-4o-mini)

- Average cost per customer service interaction: **$0.001 - $0.005**
- 1,000 interactions/month: **$1 - $5/month**
- 10,000 interactions/month: **$10 - $50/month**

### n8n Costs

- **n8n Cloud Starter**: $20/month (includes workflow execution)
- **Self-hosted**: Free (requires server hosting costs)

### ROI Calculation

**Assumptions:**
- Customer service agent cost: $20/hour
- Time per customer inquiry: 5 minutes
- Automation rate: 70%

**Monthly Savings (1,000 inquiries):**
- Total time: 1,000 × 5 min = 83.3 hours
- Automated: 700 × 5 min = 58.3 hours
- **Savings: 58.3 hours × $20 = $1,166/month**
- **Net savings after costs: $1,141 - $1,161/month**

---

## Troubleshooting

### Webhook Returns Error

**Problem:** Webhook returns 500 error

**Solutions:**
1. Check that all credentials are properly configured
2. Verify OpenAI API key is valid and has credits
3. Check Google Sheets node has correct spreadsheet URL
4. Review execution logs in n8n for specific error

### No Response from AI

**Problem:** Workflow executes but no AI response

**Solutions:**
1. Verify OpenAI credential is linked to the node
2. Check OpenAI API key has available credits
3. Test the node individually using "Test step" button
4. Review OpenAI usage dashboard for rate limits

### Google Sheets Not Logging

**Problem:** Workflow completes but no row added to Google Sheet

**Solutions:**
1. Verify Google Sheets credential is authorized
2. Check spreadsheet URL is correct
3. Ensure sheet name matches exactly (case-sensitive)
4. Verify column headers match exactly as specified
5. Check that the Google Sheets node is connected in the workflow

### Confidence Score Always Default (0.5)

**Problem:** All confidence scores are 0.5

**Solutions:**
1. Check that OpenAI system prompt includes confidence score instructions
2. Verify the regex pattern in the "Extract Confidence" node is correct
3. Test OpenAI response manually to see if it includes "Confidence: X.XX"

---

## Support

For issues with:
- **n8n workflow**: Check n8n community forum or documentation
- **OpenAI API**: Visit OpenAI support or documentation
- **Google Sheets integration**: Refer to n8n Google Sheets node documentation

---

## Next Steps

Once your workflow is running smoothly:

1. **Monitor performance** for the first week
2. **Adjust confidence threshold** based on results
3. **Enhance AI instructions** with real customer patterns
4. **Add more integrations** (Slack notifications, email alerts, etc.)
5. **Create dashboards** from Google Sheets data

Congratulations! Your AI customer service automation is now live! 🎉
