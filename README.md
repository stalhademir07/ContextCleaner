# ContextCleaner

Production-ready SaaS starter for cleaning messy AI conversations, generating continuation prompts, and building optimized prompts for ChatGPT, Claude, and Gemini.

## Stack

- Next.js App Router
- TailwindCSS
- Firebase Auth with Google login
- Firestore user records and usage limits
- Stripe Checkout subscriptions and webhook plan updates
- OpenAI Responses API

## Features

- Conversation Cleaner
- Multi-AI Prompt Writer
- Zero-Thinking Prompt Builder with smart follow-up questions
- Free, Basic, Pro plans
- Daily usage reset
- Mode gating by plan
- Strict Mode and Short Mode for Pro
- Fix my prompt and Make it 10x better actions
- Copy output buttons

## Setup

### 1. Install dependencies

```bash
npm install
```

### 2. Create environment file

```bash
cp .env.example .env.local
```

Fill `.env.local` with your Firebase, Stripe, and OpenAI values.

### 3. Firebase setup

1. Create a Firebase project.
2. Enable Authentication > Sign-in method > Google.
3. Create a Firestore database.
4. Create a Firebase Admin service account key.
5. Put the project ID, client email, and private key into `.env.local`.
6. Publish `firestore.rules` in Firebase Console > Firestore > Rules.

User document shape:

```json
{
  "email": "user@example.com",
  "plan": "free",
  "dailyUsage": 0,
  "lastUsedDate": "2026-04-30"
}
```

### 4. Stripe setup

1. Create two monthly recurring prices in Stripe:
   - Basic: $5/month
   - Pro: $7/month
2. Add the price IDs to `.env.local`:
   - `STRIPE_BASIC_PRICE_ID`
   - `STRIPE_PRO_PRICE_ID`
3. Add webhook endpoint:

```text
http://localhost:3000/api/stripe/webhook
```

For production, use your deployed domain:

```text
https://your-domain.com/api/stripe/webhook
```

Subscribe the webhook to:

- `checkout.session.completed`
- `customer.subscription.updated`
- `customer.subscription.deleted`
- `customer.subscription.paused`

Put the signing secret into `STRIPE_WEBHOOK_SECRET`.

### 5. OpenAI setup

Add your OpenAI API key:

```bash
OPENAI_API_KEY=sk-your-key
OPENAI_MODEL=gpt-4.1-mini
```

### 6. Run locally

```bash
npm run dev
```

Open:

```text
http://localhost:3000
```

## Pricing behavior

| Plan | Limit | Modes | Builder | Strict/Short |
|---|---:|---|---|---|
| Free | 1/day | Universal | No | No |
| Basic | 2/day | Universal, ChatGPT | Yes | No |
| Pro | Unlimited | Universal, ChatGPT, Claude, Gemini | Full | Yes |

When a user exceeds the daily quota, the API returns:

```text
Daily limit reached. Upgrade your plan.
```

## API

### POST `/api/generate`

Request body:

```json
{
  "type": "clean",
  "content": "messy conversation here",
  "mode": "universal",
  "strict": false,
  "short": false
}
```

Headers:

```text
Authorization: Bearer <firebase_id_token>
```

Response:

```json
{
  "output": "generated prompt",
  "plan": "free",
  "dailyUsage": 1
}
```

### POST `/api/stripe/checkout`

Request body:

```json
{ "plan": "basic" }
```

Returns a Stripe Checkout URL.

### POST `/api/stripe/webhook`

Receives Stripe subscription events and updates `users/{uid}.plan`.

## Production notes

- Set all environment variables in your hosting provider.
- Use HTTPS for Stripe webhooks in production.
- Restrict Firebase Auth domains to your real domains.
- Keep Firebase Admin and Stripe secrets server-side only.
