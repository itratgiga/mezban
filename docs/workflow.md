# n8n workflow: Mezban Concierge

One webhook, `POST /webhook/mezban`, serves the website and the voice agent. `Normalize Request` reads `action`, `sessionId`, `message`, `language`, `summary`, `bookingId`. `Route by Action` then picks a path.

## 1. chat (default)
AI Agent "Mezban Concierge" (OpenAI gpt-4.1-mini, 12-message memory, structured output parser) with tools:
- Find Venues -> Supabase RPC `mz_find_venues2`
- Next Free Dates -> `mz_next_free_dates`
- Find Caterers -> `mz_find_caterers2`
- Find Decorators -> `mz_find_decorators`

Rules: show every available hall (up to 6) and mark which fit the budget, then caterers, then optional decorators, then one itemised price-range quote. Janaza/funeral is the only case where Mezban chooses and the committee is alerted. `Respond Chat` returns `{reply, options, summary}`.

## 2. confirm
`Confirm Booking in Supabase` (`mz_confirm_booking2`) -> in parallel:
- `Is Urgent?` -> `Build Alert Payload` -> `Notify Committee` (sub-workflow).
- `Needs Payment?` (ok and total > 0) -> `Create Advance Link` (30%, ref `<id>-A`) -> `Create Full Link` (100%, ref `<id>-F`) -> `Build Links` -> `Save Payment Links` (`mz_save_payments`) -> `Respond Confirm` and `Family Email Given?` -> `Send Family Confirmation` (Gmail). Otherwise -> `Respond Confirm`.

## 3. list_bookings
`List Bookings from Supabase` (`mz_list_bookings`) -> `Respond Bookings`.

## 4. payment_status
`Load Pay Links` (`mz_payment_links_json`) -> `Split Links` -> `Check Razorpay Link` (GET payment link) -> `Collect Paid` -> `Apply Payment` (`mz_apply_payment`) -> `Respond Payment` and `Newly Paid?`.
`Needs Balance Link?`: if only the advance is paid, `Create Balance Link` (ref `<id>-B`) -> `Save Balance Link` -> `Paid Email Advance`; otherwise `Paid Email Full`.

## 5. Schedules
- Every 5 Minutes: `Pending Payment Codes` (`mz_pending_codes`) -> `Recheck Payment` (calls payment_status for each unpaid booking).
- Daily 9am Reminder: `Due Reminders` (`mz_due_reminders`) -> `Send Balance Reminder` (Gmail) -> `Mark Reminded` (`mz_mark_reminded`).

## Database (Supabase)
Tables: `mz_venues`, `mz_events`, `mz_decorators`, `mz_payments` plus caterer, package, volunteer and reminder tables. Payment status values: unpaid, advance_paid, paid.

## Credentials needed in n8n
Supabase (service role), OpenAI, Gmail OAuth2, Razorpay (HTTP Basic, test key id and secret).
