# Algorithmic_Trading_Simulator

## Guidelines

### Repository Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/GC-OpenSoft-2026/Algorithmic-Trading-Simulator.git
   ```

### Working on a Feature (Git Workflow)

We use the `develop` branch for all active development. **Do not commit directly to the `main` branch.**

#### 1. Sync with `develop`

Before starting a new feature, ensure your local `develop` branch is up to date:

```bash
git checkout develop
git pull origin develop
```

#### 2. Create a Feature Branch

Create a new branch for your feature off `develop` using a descriptive name:

```bash
git checkout -b feat/your-feature-name
```

#### 3. Make Changes and Commit

Make your code changes, stage them, and commit with a clear message:

```bash
git add .
git commit -m "feat: descriptive message about your changes"
```

#### 4. Push Your Feature Branch

Once your work is complete and committed, push your branch directly to the remote repository:

```bash
git push -u origin feat/your-feature-name
```

#### 5. Open PR and Check Conflicts on GitHub

On GitHub, select the develep branch and click **Compare & pull request** for your pushed feature branch, then confirm the branch selectors are:

- **base** = `develop` (the branch you want to merge _into_)
- **compare** = `feat/your-feature-name` (the branch you want to merge _from_)

Use the compare view to review the changes and check if GitHub shows merge conflicts before creating the PR.

#### 6. If Conflicts Exist, Sync `develop` into Your Feature Branch

If the PR shows conflicts, bring the latest `develop` into your feature branch:

```bash
git checkout develop
git pull origin develop
git checkout feat/your-feature-name
git merge develop
```

Resolve conflicts in your editor, then commit and push:

```bash
git add .
git commit -m "chore: resolve merge conflicts with develop"
git push origin feat/your-feature-name
```

#### 7. Merge PR

After conflicts are resolved and checks pass, the PR can be reviewed and merged by a seniors.


---

## Backend Setup (Go Server)

### Step 1: Navigate to Backend Directory

```bash
cd server
```

---

### Step 2: Initialize Go Module

Use a single shared module path for the entire team (same as GitHub repo):

```bash
go mod init github.com/GC-OpenSoft-2026/Algorithmic-Trading-Simulator
```

---

### Step 3: Install Dependencies

Install all dependencies automatically:

```bash
go mod tidy
```

This reads imports from the codebase and installs everything (similar to `requirements.txt` in Python).

---

### Step 4: Run Docker 

Run the docker-compose file to run the databases

```bash
docker compose up
```

or if you want to free the terminal 

```bash
docker compose up -d
```

---

### Step 5: Run the Server

```bash
go run cmd/synthetic-bull/main.go
```

Server will start at:

```
http://localhost:8080
```

---

### Step 5: Test Endpoints

```bash
curl http://localhost:8080/ping
curl http://localhost:8080/health
```

---

### Step 6: WebSocket Endpoint

```
ws://localhost:8080/v1/ws
```

Use Postman or any WebSocket client to test real-time communication.

---

## Server Folder Structure (Overview)

```text
backend/
├── cmd/
│   └── synthetic-bull/
│       └── main.go
├── internal/
│   ├── matching/
│   ├── simulation/
│   ├── api/
│       └── router.go
│   ├── handlers
│   ├── auth/
│   ├── config/
│   ├── utils/
│       └── response
│   ├── database
│   └── models/
│
```

---
# Client Setup Guide

## Requirements
 - Node Installed

## Step 1
From root directory, run the code below:

```
cd client
```

## Step 2
```
npm i
```

## Step 3
```
npm run dev
```

## Architecture & Current Status

### Core Matching Engine

The backend is currently powered by an **in-memory, concurrent matching engine built in Go**.

- **Unidirectional Data Flow**  
  The REST API handles incoming requests and passes them into a buffered Go channel (`orderStream`).

- **Deterministic Execution**  
  A dedicated background goroutine processes the order queue sequentially, ensuring strict **Price-Time priority** without race conditions.

- **Background Logging**  
  Executed trades are passed through a `tradeStream` channel to a background worker for asynchronous caching and logging.

---

## API Reference

Base path: `/v1`

| Method | Path | Description |
|--------|------|-------------|
| POST | `/auth/register` | Create a new user account and issue token pair |
| POST | `/auth/login` | Authenticate and issue token pair |
| POST | `/auth/refresh` | Rotate access token using refresh token |
| POST | `/auth/logout` | Revoke session |
| GET | `/ws` | Upgrade to WebSocket connection |
| GET | `/assets` | List all tradeable assets |
| GET | `/marketdata/{symbol}` | Latest price, volume, and change metrics |
| GET | `/candles/{symbol}/{interval}` | OHLCV candle history |
| POST | `/orders/` | Place a new limit or market order |
| DELETE | `/orders/{id}` | Cancel an open order |
| GET | `/orders/{id}` | Query single order status |
| GET | `/orders/active` | List all active orders for the user |
| GET | `/orders/history` | Paginated order history |
| GET | `/trades/history` | Paginated executed trade history |
| GET | `/portfolio/history` | Equity curve and P&L history |
| POST | `/portfolio/reset` | Reset paper-trading portfolio to initial state |
| GET | `/watchlist/` | Retrieve user watchlist |
| POST | `/watchlist/{symbol}` | Add symbol to watchlist |
| DELETE | `/watchlist/{symbol}` | Remove symbol from watchlist |

Note: all `/orders`, `/trades`, `/portfolio`, and `/watchlist` endpoints require JWT authentication.
---
# Containerization & One Click Start

## Step 1: create a .env file in root
- create a .env in root folder and add the following variables

```
PRIMARY_DB_PASSWORD=local_secret_db
CANDLESTICK_DB_PASSWORD=local_secret_ts
REDIS_PASSWORD=local_secret_redis
SERVER_JWT_SECRET=local_super_secret_jwt_key
SERVER_JWT_REFRESH_SECRET=local_super_secret_refresh_key
```

## Step 2: Run the Container
- Run the Container with the following command below to start the whole application.
- Also Make Sure to compose down other conflicting containers

```
docker compose up -d --build
```

## Step 3: Check the Frontend
- You can check the frontend on url below
```
http://localhost:80
```
or
```
http://localhost
```
