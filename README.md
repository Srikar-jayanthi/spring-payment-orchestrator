# Payment Gateway Project

This project simulates a payment gateway with a backend API, a merchant dashboard, and an embedded checkout widget.

## Project Structure

```text
payment-gateway/
├── backend/                # Node.js / Express API & Worker
│   ├── src/
│   ├── Dockerfile
│   ├── Dockerfile.worker
│   └── package.json
├── checkout-widget/        # React Checkout Widget
│   ├── src/
│   ├── webpack.config.js
│   └── package.json
├── dashboard/              # React Merchant Dashboard
│   ├── public/
│   ├── src/
│   └── package.json
├── database/               # Database initialization
│   └── init.sql
├── docker-compose.yml
└── README.md
```


## Service Details

Service      | Container Name   | Port (Host:Container) | Description
------------ | ---------------- | --------------------- | -------------------------------------------------------------
postgres     | payment_db       | 5432:5432             | PostgreSQL database for storing transactions and user data
redis        | redis_gateway    | 6379:6379             | Redis instance for job queues (BullMQ) and caching
api          | payment_api      | 8000:8000             | Main backend API service
worker       | gateway_worker   | N/A                   | Background worker for async payment jobs and webhooks
checkout     | checkout_cdn     | 3001:3001             | Serves the checkout widget static files

## Environment Variables

Configured in docker-compose.yml for api and worker services.

NODE_ENV = development
PORT = 8000
DATABASE_URL = postgresql://...
REDIS_URL = redis://redis:6379
TEST_MODE = true
WEBHOOK_RETRY_INTERVALS_TEST = true

## API Reference

Base URL: http://localhost:8000/api/v1

Authentication Headers:
x-api-key: Your API Key
x-api-secret: Your API Secret

## Payments

Create Payment
POST /payments

Request Body:
{
  "amount": 1000,
  "currency": "INR",
  "method": "card",
  "order_id": "order_12345",
  "vpa": "test@upi"
}

Note: vpa is required only if method is "upi".

Response:
{
  "id": "pay_12345",
  "status": "pending"
}

Capture Payment
POST /payments/:id/capture

Response:
{
  "id": "pay_12345",
  "captured": true
}

## Refunds

Create Refund
POST /payments/:id/refunds

Request Body:
{
  "amount": 500,
  "reason": "Customer request"
}

Get Refund
GET /refunds/:id

## Webhooks

List Webhooks
GET /webhooks

Retry Webhook
POST /webhooks/:id/retry

## Running the Project

docker-compose up --build

API: http://localhost:8000
Checkout Widget: http://localhost:3001
