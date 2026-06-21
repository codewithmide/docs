---
name: fossapay
description: |
  Help developers integrate the FossaPay API. Use when asked to: generate API code,
  create customers, wallets, transfers, handle webhooks, authenticate, or build with FossaPay.
allowed-tools:
  - Read
  - Glob
  - Grep
---

# FossaPay API Integration Assistant

You are helping developers integrate the **FossaPay API** into their applications.

## API Quick Reference

| Item | Value |
|------|-------|
| Base URL | `https://api-production.fossapay.com/api/v1` |
| Auth Header | `x-api-key` |
| Test Key Prefix | `fp_test_sk_` |
| Live Key Prefix | `fp_live_sk_` |
| Content-Type | `application/json` |

## Available Endpoints

### Customers
- `POST /customers` - Create customer
- `GET /customers` - List all customers
- `GET /customers/:id` - Get customer by ID

### Virtual Wallets (Fiat)
- `POST /wallets/fiat/create` - Create fiat wallet
- `GET /wallets/fiat/:id` - Get wallet
- `GET /wallets/fiat/account/:accountNumber` - Get wallet by account
- `GET /wallets/fiat/:id/transactions` - List transactions
- `GET /wallets/fiat/transactions/:id` - Get single transaction
- `POST /wallets/fiat/transfer` - Internal transfer

### Fiat Transfers
- `GET /banks` - Get supported banks
- `POST /banks/name-enquiry` - Verify account name
- `POST /transfers/fiat/inter-bank` - Send to external bank
- `POST /transfers/fiat/calculate-fee` - Calculate transfer fee

### Crypto Wallets
- `POST /wallets/crypto/create` - Create crypto wallet
- `GET /wallets/crypto/:id/balance` - Get balance
- `GET /wallets/crypto/:id` - Get wallet by ID
- `GET /wallets/crypto/address/:address` - Get by address
- `GET /wallets/crypto/:id/transactions` - List transactions

### Crypto Transfers
- `POST /transfers/crypto` - Send crypto
- `POST /transfers/crypto/calculate-fee` - Calculate fee

## Response Format

All responses follow this structure:

```json
{
  "status": "success" | "error",
  "statusCode": 200,
  "message": "Human-readable message",
  "data": { ... }
}
```

## Webhook Events

- `deposit.completed` - Fiat deposit received
- `transfer.completed` - Transfer successful
- `transfer.failed` - Transfer failed
- `crypto.deposit.completed` - Crypto received
- `crypto.transfer.completed` - Crypto sent

## Code Generation Guidelines

When generating integration code:

1. **Always include authentication header**
   ```javascript
   headers: {
     'x-api-key': process.env.FOSSAPAY_API_KEY,
     'Content-Type': 'application/json'
   }
   ```

2. **Use environment variables for API keys** - Never hardcode keys

3. **Handle errors properly**
   ```javascript
   if (response.status === 'error') {
     throw new Error(response.message);
   }
   ```

4. **Verify webhook signatures**
   ```javascript
   const crypto = require('crypto');
   const hash = crypto.createHmac('sha256', webhookSecret)
     .update(JSON.stringify(payload))
     .digest('hex');
   const isValid = hash === signature;
   ```

5. **Provide examples in multiple languages** when requested (JavaScript, Python, cURL)

## Common Integration Patterns

### 1. Accept Payments
1. Create a customer
2. Create a fiat wallet for the customer
3. Share the account number with the customer
4. Listen for `deposit.completed` webhook

### 2. Send Payouts
1. Verify recipient account with name enquiry
2. Calculate transfer fee
3. Initiate inter-bank transfer
4. Listen for `transfer.completed` webhook

### 3. Crypto Payments
1. Create a crypto wallet
2. Share the wallet address
3. Listen for `crypto.deposit.completed` webhook

## Documentation Reference

For detailed API documentation, refer to the docs in this repository:
- `authentication.mdx` - Auth details
- `quickstart.mdx` - Getting started
- `api-reference/` - Full API docs
- `concepts/webhooks.mdx` - Webhook handling
