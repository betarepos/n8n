# Conference Thank You Email Webhook

## Quick Test Command

Replace `http://localhost:5678/webhook-test/conference-attendees` with your actual webhook URL from the activated workflow.

```bash
curl -X POST "http://localhost:5678/webhook-test/conference-attendees" \
  -H "Content-Type: application/json" \
  -d '{
    "attendees": [
      {
        "name": "John Doe",
        "email": "john.doe@example.com"
      },
      {
        "name": "Jane Smith", 
        "email": "jane.smith@example.com"
      },
      {
        "name": "Mike Johnson",
        "email": "mike.johnson@example.com"
      }
    ]
  }'
```

## Template for Copy-Paste During Conference

```bash
curl -X POST "http://localhost:5678/webhook-test/conference-attendees" \
  -H "Content-Type: application/json" \
  -d '{
    "attendees": [
      {
        "name": "Benjamin Tasche",
        "email": "bt@beta-its.de"
      },
      {
        "name": "Benjamin Tasche",
        "email": "bt@digitalmindset.de"
      }
    ]
  }'
```

## Single Attendee Test

```bash
curl -X POST "http://localhost:5678/webhook-test/conference-attendees" \
  -H "Content-Type: application/json" \
  -d '{
    "attendees": [
      {
        "name": "Test User",
        "email": "test@example.com"
      }
    ]
  }'
```

## Notes

- Replace `http://localhost:5678/webhook-test/conference-attendees` with the actual webhook URL from your n8n workflow
- The webhook URL will be something like: `https://your-n8n-instance.com/webhook/conference-attendees`
- Add as many attendees as needed to the `attendees` array
- Make sure the workflow is **active** before sending the webhook
