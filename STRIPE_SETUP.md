# Stripe Integration Setup Guide
## Bible for Life Stages - Direct Stripe Integration

This is a simpler alternative to RevenueCat that connects directly to Stripe.

---

## What You Need

1. A Stripe account (free to create)
2. Two subscription prices in Stripe
3. Environment variables configured

---

## Step 1: Create/Access Stripe Account

1. Go to [dashboard.stripe.com](https://dashboard.stripe.com)
2. Sign up or log in
3. Complete account setup (for live payments you'll need bank info, but test mode works immediately)

---

## Step 2: Create Products & Prices in Stripe

### In the Stripe Dashboard:

1. Go to **Products** (in the left sidebar)
2. Click **+ Add product**

### Create Product:
- **Name**: Bible for Life Stages Premium
- **Description**: Full access to all premium features

### Add Two Prices:

**Price 1 - Monthly:**
- Click **Add a price**
- **Pricing model**: Standard pricing
- **Price**: $5.00
- **Billing period**: Monthly
- **Price ID**: Copy this! (starts with `price_`)

**Price 2 - Yearly:**
- Click **Add another price**
- **Pricing model**: Standard pricing
- **Price**: $45.00
- **Billing period**: Yearly
- **Price ID**: Copy this! (starts with `price_`)

---

## Step 3: Get Your API Keys

1. Go to **Developers > API keys** in Stripe
2. Copy your **Secret key** (starts with `sk_test_` for test mode or `sk_live_` for production)
3. Copy your **Publishable key** if needed (starts with `pk_`)

---

## Step 4: Update Environment Variables

Edit your `.env.local` file:

```env
# Stripe Configuration
STRIPE_SECRET_KEY=sk_test_xxxxxxxxxxxxxxxxxxxxx
NEXT_PUBLIC_STRIPE_PRICE_MONTHLY=price_xxxxxxxxxxxxxxxxxxxxx
NEXT_PUBLIC_STRIPE_PRICE_YEARLY=price_xxxxxxxxxxxxxxxxxxxxx

# Your app URL (update for production)
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

---

## Step 5: Switch to Stripe Context

You have two options:

### Option A: Replace the context file (recommended)
```bash
# Backup the RevenueCat version
mv context/subscription-context.tsx context/subscription-context-revenuecat-backup.tsx

# Use the Stripe version
mv context/subscription-context-stripe.tsx context/subscription-context.tsx
```

### Option B: Update the import in layout.tsx
Change the import to use the Stripe context directly.

---

## Step 6: Switch to Stripe Subscription Page

```bash
# Backup the current page
mv app/subscription/page.tsx app/subscription/page-revenuecat.tsx

# Use the Stripe version  
mv app/subscription/page-stripe.tsx app/subscription/page.tsx
```

---

## Step 7: Install Stripe Package

```bash
cd "C:\Users\steve\Documents\Life Stages - Bible"
npm install stripe
```

---

## Step 8: Test It!

1. Start your dev server: `npm run dev`
2. Go to `/subscription`
3. Click "Start 7-Day Free Trial"
4. Use Stripe test card: `4242 4242 4242 4242` (any future date, any CVC)
5. Complete checkout
6. You should be redirected back with premium access!

---

## Step 9: Set Up Webhook (for production)

Webhooks let Stripe notify your app about subscription changes.

1. Go to **Developers > Webhooks** in Stripe
2. Click **Add endpoint**
3. Enter your URL: `https://your-domain.com/api/stripe/webhook`
4. Select events:
   - `checkout.session.completed`
   - `customer.subscription.created`
   - `customer.subscription.updated`
   - `customer.subscription.deleted`
   - `invoice.paid`
   - `invoice.payment_failed`
5. Copy the **Signing secret** (starts with `whsec_`)
6. Add to `.env.local`:
```env
STRIPE_WEBHOOK_SECRET=whsec_xxxxxxxxxxxxxxxxxxxxx
```

---

## Test Cards

| Card Number | Result |
|-------------|--------|
| 4242 4242 4242 4242 | Success |
| 4000 0000 0000 0002 | Declined |
| 4000 0000 0000 3220 | 3D Secure required |

Use any future expiration date and any 3-digit CVC.

---

## Going Live

1. Complete Stripe account verification
2. Switch from test keys (`sk_test_`) to live keys (`sk_live_`)
3. Create live prices (or use the same product in live mode)
4. Update all environment variables with live values
5. Set up webhook for your production URL
6. Deploy!

---

## Files Created

| File | Purpose |
|------|---------|
| `lib/stripe.ts` | Stripe configuration & helpers |
| `app/api/stripe/checkout/route.ts` | Creates checkout sessions |
| `app/api/stripe/webhook/route.ts` | Handles Stripe webhooks |
| `app/api/stripe/portal/route.ts` | Customer billing portal |
| `context/subscription-context-stripe.tsx` | React context for subscriptions |
| `app/subscription/page-stripe.tsx` | Updated subscription page |

---

## Quick Checklist

- [ ] Stripe account created
- [ ] Product created with monthly + yearly prices
- [ ] Price IDs copied to .env.local
- [ ] Secret key copied to .env.local
- [ ] `npm install stripe` completed
- [ ] Context switched to Stripe version
- [ ] Subscription page switched to Stripe version
- [ ] Test purchase completed
- [ ] Webhook configured (for production)

---

## Troubleshooting

**"Price not configured" error:**
- Make sure NEXT_PUBLIC_STRIPE_PRICE_MONTHLY and NEXT_PUBLIC_STRIPE_PRICE_YEARLY are set
- Restart your dev server after changing .env.local

**Checkout doesn't redirect:**
- Check browser console for errors
- Verify STRIPE_SECRET_KEY is correct

**Subscription not showing after checkout:**
- The localStorage-based solution works for testing
- For production, implement proper webhook handling

---

## Need Help?

- [Stripe Docs](https://stripe.com/docs)
- [Stripe Checkout](https://stripe.com/docs/payments/checkout)
- [Stripe Billing](https://stripe.com/docs/billing)
