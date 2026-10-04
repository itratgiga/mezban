# Mezban

Event-booking assistant for the Shia Ithna Ashari community in Mumbai. Hackathon entry by **Team Zeenat-E-Zainab**: Itrat Giga, Sayyeda Hemani, Mariam Meghani, Zia Fatima Shroff.

Live site: https://mezban-app-sigma.vercel.app

## What it does
- Chat and voice concierge: the client gives event, date, guests, food and budget, and Mezban shows several halls, caterers and decorators to choose from (price ranges only).
- Confirm booking: saved in Supabase, committee alerted for urgent (janaza) cases, confirmation email sent.
- Payments: Razorpay payment links (test mode) for 30% advance or 100% full payment. Paid status shows in chat and by email. Advance payers get the balance link and a daily 9am reminder.
- Bookings tab with Paid / Advance paid / Unpaid badges.

## Stack
| Part | Tool |
|---|---|
| Front-end | Single-file HTML (`index.html`), hosted on Vercel |
| Automation / AI agent | n8n workflow "Mezban Concierge" (webhook `POST /webhook/mezban`) |
| Database | Supabase (Postgres + RPC functions) |
| Voice | Vapi assistant (web SDK, public key only in the page) |
| Email | Gmail (mezbanteam.official@gmail.com) |
| Payments | Razorpay Payment Links (test mode) |

## Files
- `index.html` - the whole app (UI, chat, voice, payments panel).
- `logo.jpg` - Mezban logo.
- `docs/workflow.md` - the n8n workflow, node by node.

## Deploy
Upload `index.html` and `logo.jpg` together to any static host (Vercel, Netlify, GitHub Pages).

## Notes
- No secret keys are stored in this repo. Supabase service key, OpenAI, Gmail and Razorpay credentials live inside n8n.
- Razorpay test card: 5267 3181 8797 5449, any future expiry, any CVV, OTP success.
- Some decorators are labelled "Sample" and are demo entries.
