# Shopify AI Customer Service Automation Workflow

> **Production-ready n8n workflow for automating customer service using OpenAI GPT-4o-mini**

Built 100% from official n8n documentation. No custom nodes. No assumptions. No guesswork.

---

## 🎯 What This Workflow Does

This n8n workflow automates customer service for beauty/skincare e-commerce stores by:

1. **Receives customer questions** via webhook (from contact forms, chat widgets, etc.)
2. **Processes with AI** using OpenAI GPT-4o-mini to generate helpful responses
3. **Routes intelligently** based on AI confidence level:
   - **High confidence (≥80%)**: Sends automated response directly to customer
   - **Low confidence (<80%)**: Flags for human review with AI suggestion
4. **Logs everything** to Google Sheets for tracking and analysis
5. **Returns response** to the webhook caller (website, app, etc.)

---

## 📊 Demo Scenario

**Use Case:** "Glow Beauty" - Online Skincare Store

**Customer Question:**
> "What is your return policy for opened skincare products?"

**Workflow Response:**
```json
{
  "status": "success",
  "automated": true,
  "message": "Thank you for your question! According to our return policy, we accept returns within 30 days for unopened products. Unfortunately, opened skincare products cannot be returned due to health and safety regulations. However, if you're experiencing issues with a product, please contact our support team at support@glowbeauty.com - they may be able to help!",
  "customerName": "Jane Doe",
  "confidenceScore": 0.95,
  "timestamp": "2025-01-15T10:30:00Z"
}
```

**Behind the scenes:**
- AI confidence: 95% (high confidence)
- Route: Automated response
- Logged to Google Sheets
- Response time: ~2-3 seconds

---

## 🎯 Target Performance

- **Automation Rate:** 70% (7 out of 10 questions automated)
- **Time Saved:** 3 hours/day (for stores handling ~100 inquiries/day)
- **Response Time:** 2-5 seconds (vs 5-30 minutes for human agents)
- **Cost:** $1-5/month for 1,000 interactions (OpenAI API)

---

## 🏗️ Workflow Architecture

```
Customer Question
       ↓
   Webhook Trigger
       ↓
   Process Data (Code)
       ↓
   OpenAI GPT-4o-mini
       ↓
   Extract Confidence (Code)
       ↓
   IF Confidence ≥ 0.8?
       ↓
    ┌──┴──┐
   YES   NO
    ↓     ↓
  Send   Flag for
  Auto   Human
  Reply  Review
    ↓     ↓
    └──┬──┘
       ↓
  Log to Google Sheets
       ↓
  Return Response
```

---

## 📦 What's Included

### Files in This Package:

1. **`shopify-ai-workflow.json`** - Complete n8n workflow (import this!)
2. **`SETUP.md`** - Step-by-step setup guide (start here!)
3. **`VALIDATION-REPORT.md`** - Documentation validation proof
4. **`README.md`** - This file

---

## 🚀 Quick Start

### 1. Prerequisites

- n8n instance (Cloud or self-hosted)
- OpenAI API account ([Get one here](https://platform.openai.com/))
- Google account (for Google Sheets logging)

### 2. Installation (5 minutes)

```bash
# 1. Download the workflow
# Already have it? Great!

# 2. Import into n8n
# - Open n8n
# - Click "Add workflow" → "Import from File"
# - Select shopify-ai-workflow.json

# 3. Follow setup guide
# See SETUP.md for detailed instructions
```

### 3. Configuration

**Required:**
- OpenAI API key → Add to n8n credentials
- Google Sheets → Create spreadsheet, add credentials
- Webhook URL → Copy from workflow

**Optional:**
- Customize store context (name, policies, products)
- Adjust confidence threshold (default: 0.8)
- Modify AI instructions

**Detailed instructions:** See `SETUP.md`

---

## 🧪 Testing the Workflow

### Test with cURL:

```bash
curl -X POST https://your-n8n-instance.com/webhook/shopify-customer-service \
  -H "Content-Type: application/json" \
  -d '{
    "customerName": "Jane Doe",
    "customerEmail": "jane@example.com",
    "customerMessage": "What is your return policy?",
    "timestamp": "2025-01-15T10:30:00Z"
  }'
```

### Expected Response:

**High Confidence (Automated):**
```json
{
  "status": "success",
  "automated": true,
  "message": "AI generated response here...",
  "confidenceScore": 0.95
}
```

**Low Confidence (Human Review):**
```json
{
  "status": "pending_review",
  "automated": false,
  "message": "Thank you! Our team will respond within 2-4 hours.",
  "flaggedForReview": true,
  "aiSuggestion": "Suggested answer for human agent..."
}
```

---

## 📋 Nodes Used (All Built-in)

| Node | Purpose | Documentation |
|------|---------|---------------|
| **Webhook** | Receive customer questions | Trigger node |
| **Code** (×2) | Process data, extract confidence | JavaScript execution |
| **OpenAI** | Generate AI responses | GPT-4o-mini integration |
| **IF** | Route based on confidence | Conditional logic |
| **Set** (×2) | Prepare responses | Data transformation |
| **Google Sheets** | Log all interactions | Data storage |

**Total:** 8 nodes, 0 custom nodes

---

## 💡 Use Cases

This workflow is perfect for:

### E-commerce Stores:
- Product information questions
- Shipping policy inquiries
- Return/exchange questions
- Order status (with integration)

### SaaS Companies:
- Billing questions
- Feature inquiries
- Account management
- Technical support (tier 1)

### Service Businesses:
- Appointment scheduling
- Service offerings
- Pricing questions
- General FAQs

---

## 📊 ROI Calculator

### Example: Small E-commerce Store

**Assumptions:**
- 100 customer inquiries/day
- 5 minutes per inquiry (manual)
- Agent cost: $20/hour
- 70% automation rate

**Monthly Calculations:**
```
Total inquiries:        3,000/month
Manual time:           3,000 × 5 min = 250 hours
Automated (70%):       2,100 inquiries = 175 hours saved
Agent cost saved:      175 hours × $20 = $3,500/month

OpenAI cost:           3,000 × $0.002 = $6/month
n8n Cloud cost:        $20/month

NET SAVINGS:           $3,474/month ($41,688/year)
```

**ROI: 13,380% 🚀**

---

## 🔒 Security & Privacy

### Data Handling:

✅ **Customer Data:**
- Processed in real-time
- Logged to Google Sheets (your control)
- Not stored in n8n (unless you enable execution history)

✅ **API Keys:**
- Stored securely in n8n credentials
- Not exposed in workflow JSON
- Can be rotated anytime

✅ **Webhook Security:**
- Optional authentication (Basic Auth, Header Auth, JWT)
- IP whitelisting available
- CORS configuration supported

### Compliance:

- **GDPR:** You control data storage (Google Sheets)
- **Data Retention:** Configure as needed
- **Right to Deletion:** Delete rows from Google Sheets
- **Audit Trail:** Complete interaction log

---

## 📈 Customization Ideas

### Easy Customizations (No code):

1. **Change confidence threshold**
   - Edit IF node: Change 0.8 to 0.7 (more automation) or 0.9 (more conservative)

2. **Update store information**
   - Edit "Process Customer Data" code node
   - Change store name, policies, product categories

3. **Modify AI tone**
   - Edit OpenAI system message
   - Make it more formal, casual, technical, etc.

### Advanced Customizations (With code):

4. **Add Slack notifications**
   - Add Slack node after "Prepare Human Review Alert"
   - Notify team channel when flagged

5. **Connect to order system**
   - Add HTTP Request node to fetch order details
   - Include in context for AI

6. **Multi-language support**
   - Add language detection in Code node
   - Use different AI prompts per language

7. **Sentiment analysis**
   - Add OpenAI moderation/classification
   - Escalate urgent/angry messages

---

## 📚 Documentation

### Setup & Configuration:
👉 **See `SETUP.md`** for complete setup instructions

### Validation & Proof:
👉 **See `VALIDATION-REPORT.md`** for documentation validation

### n8n Documentation:
- [Webhook Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)
- [Code Node](https://docs.n8n.io/code/)
- [OpenAI Node](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-langchain.openai/)
- [Google Sheets Node](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.googlesheets/)

---

## 🐛 Troubleshooting

### Common Issues:

**Problem:** Webhook returns 500 error
- **Solution:** Check credentials are configured (OpenAI, Google Sheets)

**Problem:** AI always returns confidence 0.5
- **Solution:** Verify OpenAI system prompt includes confidence instructions

**Problem:** Google Sheets not logging
- **Solution:** Check spreadsheet URL and sheet name match exactly

**Problem:** High API costs
- **Solution:** Reduce maxTokens in OpenAI node or use cheaper model

👉 **See `SETUP.md` for detailed troubleshooting**

---

## 💰 Cost Breakdown

### OpenAI API Costs (gpt-4o-mini):

| Monthly Volume | Estimated Cost |
|----------------|----------------|
| 100 inquiries | $0.20 - $0.50 |
| 1,000 inquiries | $1 - $5 |
| 10,000 inquiries | $10 - $50 |
| 100,000 inquiries | $100 - $500 |

### n8n Costs:

| Plan | Cost | Included Executions |
|------|------|---------------------|
| Cloud Starter | $20/month | Unlimited |
| Self-hosted | Free | Unlimited |

### Total Monthly Cost (1,000 inquiries):
- **$21 - $25/month** (n8n Cloud + OpenAI)
- **$1 - $5/month** (Self-hosted + OpenAI)

---

## 🎓 Learning Resources

### Understanding the Workflow:

1. **Start here:** `SETUP.md` - Step-by-step setup
2. **Understand nodes:** `VALIDATION-REPORT.md` - Every parameter explained
3. **Customize:** Experiment with Code and OpenAI nodes

### n8n Resources:

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [n8n YouTube Channel](https://www.youtube.com/c/n8n-io)

### OpenAI Resources:

- [OpenAI API Documentation](https://platform.openai.com/docs/)
- [Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [GPT Best Practices](https://platform.openai.com/docs/guides/gpt-best-practices)

---

## 🤝 Contributing

Found an improvement? Have a customization to share?

### Ways to Contribute:

1. **Share your results** - How much time/money did you save?
2. **Report issues** - Found a bug or improvement?
3. **Customizations** - Built something cool? Share it!
4. **Use cases** - Using this for something unique? Let us know!

---

## 📄 License

This workflow is provided as-is for use with n8n. Built using official n8n documentation.

**Components:**
- n8n: [Sustainable Use License](https://n8n.io/license)
- OpenAI: Subject to [OpenAI Terms of Use](https://openai.com/policies/terms-of-use)
- Google Sheets: Subject to [Google Terms of Service](https://policies.google.com/terms)

---

## ⚠️ Disclaimer

This workflow uses AI to automate customer service. Always:

- ✅ Monitor AI responses for quality
- ✅ Review flagged interactions daily
- ✅ Have human oversight for complex issues
- ✅ Comply with applicable regulations (GDPR, CCPA, etc.)
- ✅ Inform customers they may be interacting with AI (if required)

AI should **augment** human customer service, not replace it entirely.

---

## 📞 Support

### For issues with:

- **This workflow:** Review `SETUP.md` and `VALIDATION-REPORT.md`
- **n8n platform:** [n8n Community](https://community.n8n.io/)
- **OpenAI API:** [OpenAI Support](https://help.openai.com/)
- **Google Sheets:** [Google Workspace Support](https://support.google.com/workspace/)

---

## 🎉 Success Stories

After implementing this workflow:

### Expected Results:
- ⏱️ **Response time:** From 30 min → 3 seconds
- 🤖 **Automation rate:** 60-80% of inquiries
- 💰 **Cost savings:** $1,000 - $5,000/month (depending on volume)
- 😊 **Customer satisfaction:** Faster responses = happier customers
- 📊 **Insights:** Complete data in Google Sheets for analysis

---

## 🚀 Get Started Now!

1. **Import** `shopify-ai-workflow.json` into n8n
2. **Follow** `SETUP.md` for configuration
3. **Test** with sample data
4. **Deploy** to production
5. **Monitor** results in Google Sheets

**Estimated setup time:** 15-30 minutes

---

## 📅 Version History

**v1.0.0** (2025-01-15)
- Initial release
- 8 nodes, 100% documented
- OpenAI GPT-4o-mini integration
- Google Sheets logging
- Confidence-based routing

---

## 🙏 Acknowledgments

Built using:
- [n8n](https://n8n.io/) - Workflow automation platform
- [OpenAI GPT-4o-mini](https://openai.com/) - AI language model
- [Google Sheets](https://sheets.google.com/) - Data logging

Special thanks to the n8n documentation team for comprehensive docs!

---

## 📬 Questions?

Review the documentation:
1. 📘 **SETUP.md** - Complete setup guide
2. 📊 **VALIDATION-REPORT.md** - Technical validation
3. 🌐 **n8n Docs** - Official documentation

---

**Ready to automate your customer service? Import the workflow and start saving time!** ⚡

---

*Built with ❤️ using 100% official n8n documentation. No custom nodes. No assumptions.*
