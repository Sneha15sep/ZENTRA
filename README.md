# ZENTRA - Web3 WhatsApp Payment Platform

## 1. Introduction

ZENTRA is an intelligent Web3 payment platform that enables seamless peer-to-peer transactions through WhatsApp. Powered by Circle's Web3 services and Temporal workflows, ZENTRA allows users to send and receive USDC payments directly through conversational AI, making blockchain transactions accessible to everyone.

**Key Highlights:**
- WhatsApp-based payment interface
- Web3 wallet integration via Circle
- AI-powered conversation flows using Cloudflare Workers AI
- Secure PIN-based authentication
- Temporal workflow orchestration for reliability

---

## 2. Project Structure

```
ZENTRA/
├── docker-compose.yaml          # Docker services (PostgreSQL, Temporal)
├── README.md                    # Project documentation
├── backend/                     # FastAPI backend application
│   ├── main.py                 # FastAPI server
│   ├── worker.py               # Temporal worker
│   ├── config.py               # Configuration management
│   ├── requirements.txt        # Python dependencies
│   │
│   ├── api/
│   │   ├── endpoints.py        # REST API endpoints
│   │   └── dependencies.py     # Dependency injection
│   │
│   ├── workflows/
│   │   ├── registration.py    # User registration workflow
│   │   └── payment.py         # Payment workflow
│   │
│   ├── activities/
│   │   ├── twilio_activities.py
│   │   ├── circle_activities.py
│   │   ├── database_activities.py
│   │   └── pin_activities.py
│   │
│   ├── models/
│   │   └── database.py        # Database models
│   │
│   ├── services/
│   │   ├── twilio_service.py
│   │   ├── circle_service.py
│   │   └── elevenlabs_service.py
│   │
│   └── utils/
│       └── security.py        # Security utilities
│
├── frontend/                  # Next.js web application
│   ├── app/
│   ├── public/
│   └── package.json
│
└── cloudflare-worker/         # Cloudflare Workers for AI
    ├── src/
    ├── package.json
    └── wrangler.toml
```

---

## 3. Features

✨ **Core Features:**

- **WhatsApp Integration**: Native WhatsApp messaging via Twilio
- **Web3 Wallets**: Circle integration for USDC wallet management
- **Secure Authentication**: PIN-based security with email verification
- **Payment Processing**: Direct peer-to-peer transfers with confirmation flows
- **Transaction History**: View all past transactions
- **Balance Inquiries**: Check wallet balance in real-time
- **AI Conversations**: Natural language processing for user commands
- **Workflow Orchestration**: Temporal ensures reliability and retryability

---

## 4. Flowchart

### System Architecture

```
┌─────────────┐
│  WhatsApp   │
│    User     │
└──────┬──────┘
       │ Message
       ↓
┌─────────────────┐
│     Twilio      │ WhatsApp Webhook
└────────┬────────┘
         │
         ↓
┌──────────────────────┐
│   FastAPI Backend    │ (Port 8000)
│  - Webhook Handler   │
│  - Request Parsing   │
└────────┬─────────────┘
         │
         ↓
┌──────────────────────┐
│   Temporal Workflow  │ Orchestration
│  - Registration      │ Engine
│  - Payment           │
└────────┬─────────────┘
         │
         ├─────────────────┬──────────────┬────────────────┐
         ↓                 ↓              ↓                ↓
    ┌─────────┐    ┌──────────────┐ ┌──────────┐  ┌────────────┐
    │ Twilio  │    │  PostgreSQL  │ │  Circle  │  │ ElevenLabs │
    │Activity │    │  Activity    │ │ Activity │  │  Activity  │
    └─────────┘    └──────────────┘ └──────────┘  └────────────┘
         │
         └─────────────────┬─────────────────────────┘
                           │
                           ↓
                    ┌─────────────┐
                    │  Response   │
                    │   to User   │
                    └─────────────┘
```

---

## 5. User Flow

### Registration Flow

```
1. User sends "Hi" or "Register" on WhatsApp
                    ↓
2. Bot sends 6-digit verification code
   (Auto-detected by WhatsApp)
                    ↓
3. Code verified in database
                    ↓
4. Bot sends secure PIN setup link
                    ↓
5. User completes PIN setup on web portal
                    ↓
6. Bot creates Circle wallet for user
                    ↓
7. Bot sends welcome message
   "Welcome to ZENTRA! Your wallet is ready."
```

### Messaging & Commands

```
User Commands:
- "Balance" → Display current wallet balance
- "Send $20 to Alice" → Initiate payment
- "Transactions" → View transaction history
- "Help" → Display available commands
```

---

## 6. Payment Flow

```
1. User sends: "Send $20 to Alice"
                    ↓
2. Bot parses intent and validates:
   - Sender has sufficient balance
   - Recipient exists in system
                    ↓
3. Bot requests confirmation:
   "Send $20 to Alice?"
   Reply with: CONFIRM or CANCEL
                    ↓
4. User replies: "CONFIRM"
                    ↓
5. Payment Workflow executes:
   a) Lock sender's balance
   b) Call Circle API to transfer USDC
   c) Update transaction record
   d) Release balance lock
                    ↓
6. Both users receive transaction receipt:
   "Transaction successful!"
   "Txn ID: 0x123abc..."
   "New Balance: $80.00"
```
