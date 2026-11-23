# NOTES

Fill in every field below before you submit. We read this file first: a submission with an empty field is not graded.
Write in your own words. If you did not fix something, set Status to `not fixed` and say what you found and tried; that still counts.
Status is one of: `fixed`, `partly fixed`, `not fixed`. Commits: the short hashes of the commits for this item, or `none`.

## 1. Report

> Finance says that on some days the revenue in the daily report is higher than what the payment provider paid out, and a few orders show more money received than their total.

**Status:** TODO: Fixed (after Duplicates webhook text cases Passed)

**What I saw:** TODO: The same successful payment webhook could be received multiple times. Each delivery was inserted as a new payment record,which is increase the received amount for as order and make the daily revenue report higher than the actual amount received.

**Root cause:** TODO: There is no UNIQUE for event_id so, the payment handler did not check whether a webhook event ID had already been processed. The database also allowed duplicate event ID's

**Fix and why:** TODO: I added a check to see whether the event ID already exists before inserting a payment. If the payment is already proccessed, handler returns without inserting it again. I also added UNIQUE constraint to the event_id to prevent duplicate records at the database level.

**How I verified it:** TODO: I added a new test named 'test_duplicate_payment_webhook'. It sends the same successful payment event twice and verifies that the payment amount counted only once. I ran the automated tests to check the Changes.

**Commits:** TODO: fill in

## 2. Report

> Products show as sold out, or nearly, while the shelf is full. Staff say it gets worse on days when many customers abandon checkout.

**Status:** TODO: fill in

**What I saw:** TODO: fill in

**Root cause:** TODO: fill in

**Fix and why:** TODO: fill in

**How I verified it:** TODO: fill in

**Commits:** TODO: fill in

## 3. Report

> Customers in taxed regions who used a coupon say the tax on their receipt is too high. Receipts without a coupon are right.

**Status:** TODO: fill in

**What I saw:** TODO: fill in

**Root cause:** TODO: fill in

**Fix and why:** TODO: fill in

**How I verified it:** TODO: fill in

**Commits:** TODO: fill in

## 4. Report

> Our shop in India (UTC+5:30) says sales taken in the morning do not appear in that day's report and turn up under the day before.

**Status:** TODO: fill in

**What I saw:** TODO: fill in

**Root cause:** TODO: fill in

**Fix and why:** TODO: fill in

**How I verified it:** TODO: fill in

**Commits:** TODO: fill in

## 5. Report

> Ever since we started running two server workers, we have sold more units of an item than we had, and its stock went negative.

**Status:** TODO: fill in

**What I saw:** TODO: fill in

**Root cause:** TODO: fill in

**Fix and why:** TODO: fill in

**How I verified it:** TODO: fill in

**Commits:** TODO: fill in

## 6. Open question

> Open question from support, no single right answer: sometimes a customer really does pay twice for one order (two separate payments, not a retry of the same one). Today the system records both and says nothing. Decide what should happen, build the smallest version of your decision, and explain what it costs in `NOTES.md`.

**My decision:** TODO: fill in

**Why:** TODO: fill in

**What it costs:** TODO: fill in

**What I built:** TODO: fill in

**How I verified it:** TODO: fill in

**Commits:** TODO: fill in

## Other things I noticed

Problems outside the reports: what, where, and whether you fixed them. Write `none` if there were none.

TODO: fill in

## Session logs

Which AI tools you used, and one line per file in `session_logs/`.

TODO: fill in
