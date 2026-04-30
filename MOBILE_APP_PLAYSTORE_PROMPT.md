# ContextCleaner — Mobile App + Play Store Monetization Master Prompt

Use this prompt in a fresh AI coding session after uploading the current `ContextCleaner.zip` project.

---

You are a senior mobile engineer, full-stack SaaS architect, Firebase/Stripe/Google Play Billing expert, and product designer.

I have an existing production-ready Next.js App Router SaaS called **ContextCleaner** with:

- Next.js App Router
- TailwindCSS
- Firebase Auth with Google login
- Firestore user database
- Stripe subscriptions for web checkout
- OpenAI API route for AI generation
- Plans: free, basic, pro
- Usage rules stored in Firestore

Your job is to convert this into a **Play Store-ready Android mobile app** while preserving the existing web SaaS.

## Critical Business Requirement

ContextCleaner is an AI productivity system that:

1. Cleans messy AI conversations into continuation prompts
2. Generates optimized prompts for ChatGPT, Claude, and Gemini
3. Uses a Zero-Thinking Prompt Builder that asks smart follow-up questions before creating the final prompt

## Important Payment Rule

Do **not** hardcode IBAN, bank details, card details, or private payment information anywhere in the app or repository.

Bank payout details must only be configured inside official payout dashboards such as:

- Stripe Dashboard for web revenue
- Google Play Console / Google Payments profile for Android revenue

The app itself must only contain product IDs, public config, and server-side validation logic.

## Monetization Architecture

The project must support two payment channels:

### 1. Web

Use existing Stripe subscriptions:

- Basic: $5/month
- Pro: $7/month

Stripe webhook updates Firestore:

```ts
users/{uid}.plan = "basic" | "pro"
```

### 2. Android Play Store

Implement Google Play Billing subscriptions:

- `basic_monthly` → Basic plan → $5/month equivalent
- `pro_monthly` → Pro plan → $7/month equivalent

After purchase:

1. Android app receives purchase token
2. Android app sends purchase token to backend
3. Backend verifies token with Google Play Developer API
4. Backend updates Firestore user plan
5. App refreshes user plan and unlocks features

Firestore remains the single source of truth:

```ts
users/{uid} = {
  email: string,
  plan: "free" | "basic" | "pro",
  dailyUsage: number,
  lastUsedDate: string,
  paymentProvider?: "stripe" | "google_play",
  googlePlay?: {
    productId: string,
    purchaseTokenHash: string,
    latestOrderId?: string,
    expiryTimeMillis?: string,
    autoRenewing?: boolean,
    status?: "active" | "expired" | "canceled" | "grace_period" | "on_hold"
  }
}
```

## Preferred Mobile Strategy

Create a Play Store-ready Android app using **React Native + Expo** OR **Capacitor**.

Choose the best option for fastest reliable release.

Recommended decision:

- If speed is the priority: use Capacitor wrapper around the existing Next.js deployed app.
- If native billing UX is required immediately: use React Native/Expo or bare React Native with Google Play Billing.

For this task, implement the most practical production path:

### Required Mobile App Behavior

- Google login with Firebase
- Dashboard UI adapted for mobile
- Conversation Cleaner
- Prompt Writer
- Prompt Builder question flow
- Plan gating
- Usage display
- Upgrade screen
- Google Play subscription flow
- Restore purchases button
- Copy output button
- Loading/error states
- Clean mobile design

## Required Backend API Routes

Add these backend routes to the existing Next.js app:

### POST `/api/mobile/google-play/verify`

Input:

```json
{
  "idToken": "firebase_id_token",
  "productId": "basic_monthly | pro_monthly",
  "purchaseToken": "google_purchase_token"
}
```

Behavior:

- Verify Firebase ID token
- Verify purchase with Google Play Developer API
- Map product ID to plan
- Update Firestore user plan
- Store purchase metadata safely
- Never store raw purchase token; store a SHA-256 hash
- Return updated plan

### POST `/api/mobile/google-play/sync`

Behavior:

- Verify user
- Re-check current subscription status
- Downgrade to free if expired/canceled and no active Stripe subscription exists
- Return current plan

### POST `/api/mobile/google-play/webhook` or Pub/Sub handler

Behavior:

- Accept Real-Time Developer Notifications from Google Play
- Verify notification authenticity where applicable
- Update Firestore subscription status
- Update user plan accordingly

## Required Environment Variables

Extend `.env.example` with:

```env
GOOGLE_PLAY_PACKAGE_NAME=com.contextcleaner.app
GOOGLE_PLAY_SERVICE_ACCOUNT_CLIENT_EMAIL=
GOOGLE_PLAY_SERVICE_ACCOUNT_PRIVATE_KEY=
GOOGLE_PLAY_BASIC_PRODUCT_ID=basic_monthly
GOOGLE_PLAY_PRO_PRODUCT_ID=pro_monthly
```

## Android Product IDs

Use these product IDs exactly:

```txt
basic_monthly
pro_monthly
```

## Plan Rules

Keep these rules exactly:

FREE:
- 1 request/day
- Universal mode only
- No prompt builder

BASIC:
- 2 requests/day
- Universal + ChatGPT modes
- Prompt Builder limited

PRO:
- Unlimited usage
- Universal + ChatGPT + Claude + Gemini modes
- Prompt Builder full
- Strict Mode
- Short Mode

If usage exceeded, return exactly:

```txt
Daily limit reached. Upgrade your plan.
```

## AI Modes

Keep separate internal templates for:

- universal
- chatgpt
- claude
- gemini

Do not collapse them into one generic prompt.

## Deliverables

You must provide:

1. Full updated file structure
2. Complete backend files
3. Complete mobile app files
4. Updated `.env.example`
5. Updated `README.md`
6. Play Store setup guide
7. Google Play Billing setup guide
8. Firebase setup guide
9. Stripe web setup guide
10. Test checklist

## Code Rules

- No partial code
- No pseudo code
- No missing imports
- No placeholders like “implement later”
- No broken TypeScript
- No invented secrets
- No IBAN or bank details in source code
- Everything must run after setup

## Commands Must Work

For web:

```bash
npm install
npm run dev
```

For mobile, provide exact commands depending on chosen stack.

## Final Goal

A real monetized SaaS system where:

- Web users can pay through Stripe
- Android users can pay through Google Play subscriptions
- Both payment channels update the same Firestore plan field
- The app can be published to Google Play
- ContextCleaner remains clean, scalable, and production-ready

Start by inspecting the uploaded project, then update it file-by-file.
