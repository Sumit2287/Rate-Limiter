# Rate Limiter

A React frontend and Node.js/Express API for checking API request limits with a Redis-backed token bucket and MongoDB request logging.

## Prerequisites

- Node.js 22 or later
- MongoDB running on `localhost:27017`
- Redis running on `localhost:6379`

Docker Compose is also available in `backend/docker-compose.yaml` if you prefer containerized MongoDB and Redis.

## Configuration

Create the backend environment file:

```powershell
cd backend
Copy-Item .env.example .env
```

Update `backend/.env` with local values:

```env
PORT=5000
ENV=development
FRONTEND_URI=http://localhost:5173
MONGO_URI=mongodb://localhost:27017/api-rate-limiter
REDIS_HOST=localhost
REDIS_PORT=6379
JWT_SECRET=replace_with_a_secure_secret
SALT_KEY=replace_with_a_secure_secret
```

Create `frontend/.env`:

```env
VITE_API_URL=http://localhost:5000
```

Never commit `.env` files or production secrets. The tracked `.env.example` file contains placeholders only.

## Install dependencies

```powershell
cd backend
npm install

cd ..\frontend
npm install
```

## Run locally

Open two terminals.

Terminal 1:

```powershell
cd "D:\RESOURCES\PROJECTS\Rate Limiter\backend"
npm start
```

Terminal 2:

```powershell
cd "D:\RESOURCES\PROJECTS\Rate Limiter\frontend"
npm run dev -- --host 127.0.0.1
```

Open <http://127.0.0.1:5173>.

The API health endpoint is available at <http://localhost:5000>.

## Use the rate limiter

1. Create an account and log in through the frontend.
2. Generate an API key from the Profile page.
3. Send a request to `POST http://localhost:5000/api/check`:

```powershell
$headers = @{
    "Content-Type" = "application/json"
    "x-api-key" = "YOUR_API_KEY"
}

$body = @{ reqCost = 1 } | ConvertTo-Json

Invoke-RestMethod `
    -Uri "http://localhost:5000/api/check" `
    -Method POST `
    -Headers $headers `
    -Body $body
```

Allowed requests return HTTP `200`; requests that exceed the bucket return HTTP `429`.
Open the Dashboard and click Refresh to view request totals and recent logs.

## Validate

Frontend build:

```powershell
cd frontend
npm run build
```

Frontend lint:

```powershell
npm run lint
```
