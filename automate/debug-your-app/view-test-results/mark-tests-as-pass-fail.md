# Mark tests as passed or failed #

You can mark tests as passed or failed, using the REST API. You can also pass a reason for failure:

```curl -u "USERNAME:ACCESS_KEY" -X PUT -H "Content-Type: application/json" -d "{\"status\":\"<new-status>\", \"reason\":\"<reason text>\"}" https://api.browserstack.com/automate/sessions/<session-id>.json```