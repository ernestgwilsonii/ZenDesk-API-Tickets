# ZenDesk Ticket API with curl

### Getting Started
1. Setup a free trial developer [ZenDesk account](https://developer.zendesk.com/documentation/ticketing/getting-started/zendesk-api-quick-start/)
2. Create an API token for use with 
    - Open your browser to YourAccount.zendesk.com/admin
    - Click on "Apps and integrations" from the left side menu
    - Click on "ZenDesk API" under the "APIs" section from the left side menu
    - Click on the "Settings" tab
    - Enable "Token access" (slider to the right)
    - Click the "Add API token" button and "Create a new token"
    - Give it a name (API token description)
    - Copy and save the API token to a safe place

### Use curl on your Linux OS to view and update some tickets
```
# Install "jq" to help make the JSON output more readable
# yum install - y jq
# or
# apt-get install -y jq

# Set ZenDesk API token variable
ZENDESK_API_TOKEN=2fQIX58qY6bBQwbEblqrNMfkhg0j14wcd8jmNybM
echo $ZENDESK_API_TOKEN

# Set the email address that goes with the API token using single quotes
API_EMAIL_ADDRESS='YourApiEmail@gmail.com'
echo $API_EMAIL_ADDRESS

# Set the ZenDesk URL for your account
ZENDESK_URL=https://radiofanclub.zendesk.com
echo $ZENDESK_URL

# Confirm your email and API token are working
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/users/me.json
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/users/me.json | jq

# List all tickets
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/tickets | jq

# Review ticket id 1
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/tickets/1.json | jq

# Review "open" tickets
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json?query=type%3Aticket+status%3Aopen | jq

# Review "pending" tickets
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json?query=type%3Aticket+status%3Apending | jq

# Review "solved" tickets
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json?query=type%3Aticket+status%3Asolved | jq

# REF: https://developer.zendesk.com/documentation/ticketing/using-the-zendesk-api/searching-with-the-zendesk-api/
# Search for any tickets based on a text string
SEARCH_STRING="Sample ticket"
echo $SEARCH_STRING
curl --get -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json \
  --data-urlencode "query=$SEARCH_STRING" | jq

# Search for "open" tickets based on a text string
SEARCH_STRING="Sample ticket"
echo $SEARCH_STRING
curl --get -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json \
  --data-urlencode "query=$SEARCH_STRING status:open" | jq

# Search for "pending" tickets based on a text string
SEARCH_STRING="Sample ticket"
echo $SEARCH_STRING
curl --get -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json \
  --data-urlencode "query=$SEARCH_STRING status:pending" | jq

# Search for "solved" tickets based on a text string
SEARCH_STRING="Sample ticket"
echo $SEARCH_STRING
curl --get -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/search.json \
  --data-urlencode "query=$SEARCH_STRING status:solved" | jq

# Update and Pend
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/tickets/2.json \
  -H "Content-Type: application/json" \
  -d '{"ticket": {"status": "pending", "comment": { "body": "Setting to pending."}}}' \
  -v -X PUT

# Update and Pend setting a specific author_id
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/tickets/2.json \
  -H "Content-Type: application/json" \
  -d '{"ticket": {"status": "pending", "comment": { "body": "Setting to pending.", "author_id": 494820284 }}}' \
  -v -X PUT

# Update and Solve
# REF: https://developer.zendesk.com/api-reference/ticketing/tickets/tickets/#update-ticket
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/tickets/3.json \
  -H "Content-Type: application/json" \
  -d '{"ticket": {"status": "solved", "comment": { "body": "Setting to solved."}}}' \
  -v -X PUT

# Update and solve many tickets at once by ticket IDs
curl -u $API_EMAIL_ADDRESS/token:$ZENDESK_API_TOKEN $ZENDESK_URL/api/v2/tickets/update_many.json \
  -H "Content-Type: application/json" \
  -d '{"tickets": [{"id": 1, "status": "solved", "comment": { "body": "Setting to solved."}}, {"id": 2, "status": "solved", "comment": { "body": "Setting to solved."}}]}' \
  -v -X PUT
```
