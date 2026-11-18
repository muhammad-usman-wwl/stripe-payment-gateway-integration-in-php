# Stripe Payment Gateway Integration in PHP

A complete, working example of accepting payments with **Stripe Checkout** in plain PHP — from the tutorial *"Stripe Payment Gateway Integration in PHP"* by **Simplified Dev Code Camp**.

Your site never touches card details. Your server creates a *Checkout Session*, Stripe collects the payment on its own secure hosted page, and your server verifies the result twice: on the success page and through a signed webhook.

## What is included

| File | What it does |
|---|---|
| `index.php` | Product page with "Pay with Stripe" buttons |
| `products.php` | Product catalogue — prices live server-side only, in cents |
| `config.php` | Your Stripe keys + base URL (edit this file) |
| `create-checkout-session.php` | Creates the Checkout Session, redirects to Stripe |
| `success.php` | Verifies the session with Stripe before saying "paid" |
| `cancel.php` | Shown when the customer closes checkout |
| `webhook.php` | Receives `checkout.session.completed`, verifies the signature, fulfils the order |
| `stripe-php/` | The official Stripe PHP SDK (v23), bundled — no Composer needed |

> Prefer Composer? Delete the `stripe-php/` folder and run `composer require stripe/stripe-php`, then replace the `require .../stripe-php/init.php` lines with `require 'vendor/autoload.php';`.

## Setup (5 minutes)

1. **Create a free Stripe account** at https://stripe.com — test mode works immediately, no business documents needed.
2. In the Stripe Dashboard (with **Test mode ON**) go to **Developers → API keys** and copy your **Publishable key** and **Secret key**.
3. Open `config.php` and paste **your own** keys. Never use keys from a video, screenshot, or anyone else — and never commit your secret key to GitHub.
4. Set `BASE_URL` in `config.php` to where the project runs.
5. Run it:
   - Built-in server: `php -S localhost:8000` then open http://localhost:8000
   - XAMPP: copy the folder into `htdocs`, start Apache, open http://localhost/your-folder

## Test the payment

Click **Pay with Stripe** and use Stripe's official test card:

- Card: `4242 4242 4242 4242`
- Expiry: any future date · CVC: any 3 digits · ZIP: any value

No real money moves in test mode. Try `4000 0000 0000 0002` to see a declined card.

## Webhooks (local testing)

Install the [Stripe CLI](https://docs.stripe.com/stripe-cli), then:

```bash
stripe login
stripe listen --forward-to localhost:8000/webhook.php
```

The CLI prints a signing secret (`whsec_...`) — put it in `config.php` as `STRIPE_WEBHOOK_SECRET`. Complete a test payment and watch the event arrive. On a live server, instead create the endpoint in **Dashboard → Developers → Webhooks** for the event `checkout.session.completed`.

## Go-live checklist

- [ ] Replace test keys (`sk_test_...`) with live keys (`sk_live_...`) — keep them out of Git
- [ ] Create the live webhook endpoint and update `STRIPE_WEBHOOK_SECRET`
- [ ] Set `BASE_URL` to your real HTTPS domain
- [ ] Fulfil orders in `webhook.php` (database update / email), not only on the success page
- [ ] Test one small real payment and one refund

## Honest note

This demo runs in Stripe **test mode** with placeholder keys. The code path was tested end-to-end against Stripe's real API (a placeholder key correctly returns Stripe's `401 Invalid API Key` error, and the webhook was tested with a properly signed test event). Add your own test keys and the checkout works exactly as shown in the video.
