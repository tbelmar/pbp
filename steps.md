## UX Steps

1. Select country
2. List all banks and select bank
3. Redirected and auth user with bank, agree -> user now in our agreements
4. Check GET /api​/v2​/accounts once a day.

## Code Steps

1. Get token

```bash
curl -X POST "https://bankaccountdata.gocardless.com/api/v2/token/new/" \
        -H 'accept: application/json' \
        -H 'Content-Type: application/json' \
        -d '{
	"secret_id": "",
	"secret_key": ""
}'
```

2. Get banks in country

```bash
curl -X GET "https://bankaccountdata.gocardless.com/api/v2/institutions/?country=se" -H  'accept: application/json' -H  'Authorization: Bearer ""'
```

3. Creat agreement with select bank (pbp -> bank)

```bash
curl -X POST "https://bankaccountdata.gocardless.com/api/v2/agreements/enduser/" \
  -H  "accept: application/json" \
  -H  "Content-Type: application/json" \
  -H  "Authorization: Bearer <token>" \
  -d "{\"institution_id\": \"<bank-of-choice>\",
       \"max_historical_days\": \"180\",
       \"access_valid_for_days\": \"30\",
       \"access_scope\": [\"balances\", \"details\", \"transactions\"] }"
```

4. Create a requisition (bank -> individual)

```bash
curl -X POST "https://bankaccountdata.gocardless.com/api/v2/requisitions/" \
  -H  "accept: application/json" -H  "Content-Type: application/json" \
  -H  "Authorization: Bearer <token>" \
  -d "{\"redirect\": \"<redirect-uri>\",
       \"institution_id\": \"<bank-of-choice>\" }"
```

This returns an id that we can use for requests for `GET /api​/v2​/accounts​/{id}​/`

5. Call desired endpoints for user info

```bash
curl -X GET "https://bankaccountdata.gocardless.com/api/v2/accounts/{id from above step}/transactions/" \
  -H  "accept: application/json" \
  -H  "Authorization: Bearer <token>" \
```
