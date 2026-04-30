# ContextCleaner — Play Store Release Checklist

## 1. Web SaaS First

1. Deploy the existing Next.js app to Vercel.
2. Add production environment variables.
3. Connect Firebase Auth authorized domain.
4. Configure Stripe products and webhook.
5. Test free/basic/pro plan limits.

## 2. Android App Package

Recommended package name:

```txt
com.contextcleaner.app
```

## 3. Google Play Console

Create:

- App listing
- Privacy policy URL
- Data Safety form
- App content declarations
- Closed testing track
- Subscription products:
  - `basic_monthly`
  - `pro_monthly`

## 4. Payment Handling

Do not put IBAN or bank details inside code.

Use:

- Stripe Dashboard for web payouts
- Google Play Console payments profile for Play Store payouts

## 5. Production Payment Flow

Android purchase flow:

1. User taps upgrade
2. Google Play Billing purchase screen opens
3. App receives purchase token
4. App sends token to backend
5. Backend verifies token
6. Backend updates Firestore `users/{uid}.plan`
7. App refreshes plan

## 6. Required Backend Security

- Verify Firebase ID token on every paid route
- Verify Google Play purchase tokens server-side
- Store only SHA-256 hash of purchase token
- Do not trust client-side plan state
- Keep Firestore as source of truth

## 7. Store Review Notes

Prepare a reviewer test account:

```txt
Email: reviewer account created in Firebase
Password/login method: Google test account or Firebase test user
Test instructions: Sign in, use free generation, open upgrade screen, test subscription products in closed testing.
```

## 8. Minimum Production Assets

- App icon 512x512
- Feature graphic 1024x500
- Screenshots: phone dashboard, prompt builder, result screen, upgrade screen
- Privacy policy page
- Terms page
- Support email

## 9. Final Test Checklist

- Free user max 1/day
- Basic user max 2/day
- Pro user unlimited
- Free cannot use Prompt Builder
- Basic cannot use Claude/Gemini
- Pro can use all modes
- Strict/Short toggles only work for Pro
- Copy buttons work
- Restore purchases works
- Logout/login preserves plan
- Expired subscription downgrades correctly
