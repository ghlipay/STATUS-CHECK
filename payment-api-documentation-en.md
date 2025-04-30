# Payment Check API Documentation

## Overview

This API is used to check the status of payment and withdrawal transactions. Transaction integrators can query the latest status of their payment and withdrawal transactions through this API.

## Security

API access is secured with an API key provided in the `X-API-KEY` header. Keep your API key secure and do not share it with third parties.

## Base URL

Base URL for all API requests:

```
https://lipaykripto.com/api
```

## Endpoints

### Payment and Withdrawal Status Check

```
GET /paymentcontroller
```

This endpoint is used to query the status of payment and withdrawal transactions.

#### Query Parameters

| Parameter | Type   | Required | Description                                            |
|-----------|--------|----------|--------------------------------------------------------|
| type      | string | Yes      | Transaction type. Takes values "payment" or "withdraw" |
| paymentId | string | Yes      | Unique ID of the transaction to check                  |

#### Request Headers

| Header    | Value         | Description                            |
|-----------|---------------|----------------------------------------|
| X-API-KEY | YOUR_PC_API_KEY | Your specially provided API key       |

#### Example Requests

**Payment Status Check:**

```http
GET https://lipaykripto.com/api/paymentcontroller?type=payment&paymentId=test123
X-API-KEY: YOUR_PC_API_KEY
```

**Withdrawal Status Check:**

```http
GET https://lipaykripto.com/api/paymentcontroller?type=withdraw&paymentId=test456
X-API-KEY: YOUR_PC_API_KEY
```

#### Successful Response (200 OK)

```json
{
  "success": true,
  "data": {
    "paymentId": "test123",
    "status": "confirmed",
    "tryAmount": "1000.00"
  }
}
```

#### Response Properties

| Field          | Type    | Description                                  |
|----------------|---------|----------------------------------------------|
| success        | boolean | Indicates whether the request was successful |
| data           | object  | Object containing transaction information     |
| data.paymentId | string  | Transaction ID                               |
| data.status    | string  | Transaction status (confirmed, failed, pending) |
| data.tryAmount | string  | Transaction amount (in TRY)                  |

#### Status Values

| Value     | Description             |
|-----------|-------------------------|
| confirmed | Transaction confirmed   |
| failed    | Transaction failed      |
| pending   | Transaction pending     |

#### Error Responses

```json
{
  "success": false,
  "error": {
    "code": 401,
    "message": "Invalid API key"
  }
}
```

#### Error Codes

| Code | Description             |
|------|-------------------------|
| 400  | Invalid request parameters |
| 401  | Invalid API key         |
| 404  | Transaction not found   |
| 500  | Server error            |

## Integration Steps

### 1. Creating Requests

**Example with cURL:**

```bash
curl -X GET "https://lipaykripto.com/api/paymentcontroller?type=payment&paymentId=test123" \
     -H "X-API-KEY: YOUR_PC_API_KEY"
```

### 2. Processing Responses

When you receive a successful response, you should take appropriate actions based on the transaction status:

- **confirmed**: Transaction confirmed, you can provide service/product to the user
- **pending**: Transaction is still being processed, check again later
- **failed**: Transaction failed, inform the user