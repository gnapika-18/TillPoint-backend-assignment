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

**Commits:** TODO: TODO: 67ce727 (HEAD -> main, origin/main) Fix duplicate payment webhook processing with test case

## 2. Report

> Products show as sold out, or nearly, while the shelf is full. Staff say it gets worse on days when many customers abandon checkout.

**Status:** TODO: Partly Fixed

**What I saw:** TODO: Product Stock was reduced when an order was created, even before payment. When a pending order or cancelling the order was cancelled, the reserved stock was not returned. Abandoned orders  are keep stock unavailable.

**Root cause:** TODO: Stock was reserved only for order creation,logic went wrong during cancellation logic restored for paid orders. Pending orders were ignored.

**Fix and why:** TODO: I updated the cancellation logic to restore stock for both pending and paid orders. This can make that after unapid and order cancelled can stock restored.

**How I verified it:** TODO: Added a new test named test_cancel_pending_order_restocks. It verifies that stock returns to its original quantity after cancelling a pending order. Ran the complete test suite, and all tests passed.

**Commits:** 8161e4c (HEAD -> main, origin/main) Restore stock when pending orders are cancelled

## 3. Report

> Customers in taxed regions who used a coupon say the tax on their receipt is too high. Receipts without a coupon are right.

**Status:** TODO: Fixed 

**What I saw:** TODO: Customers in taxed regions were charged too much tax when they used a coupon. Orders without coupouns had the correct tax.

**Root cause:** TODO: Tax was calculated on the original subtotal before deducting the coupon discount.

**Fix and why:** TODO: Updated price_order() to calculate the coupon discount first then calculate tax on the discounted subtotal.This is the  customers are charged tax on the correct amount.

**How I verified it:** TODO: Added test_coupon_tax_calculated_after_discount that  checks the subtotal, discount,tax,and final total for a coupon order in California.Ran the complete test suite successfully.

**Commits:** TODO: b86f3db (HEAD -> main, origin/main) Calculate tax after applying coupon discount

## 4. Report

> Our shop in India (UTC+5:30) says sales taken in the morning do not appear in that day's report and turn up under the day before.

**Status:** TODO:Fixed 

**What I saw:** TODO: Sales made during the morning in INDIA appeared under the previous day's report instead of this correct local date.

**Root cause:** TODO: The Utc offset is added instead of subtracted when the calculating the UTC boundaries of a local calender day.

**Fix and why:** TODO: Just i updated with exchanging of the sign + to - that means day_bounds_utc() to subtract the shop UTC offset when calculating the UTC start and end of the local day.This correctly Shows INDIA UTC+5:30 timezone.

**How I verified it:** TODO:Added test_daily_sales_india_timezone, that can checks that a sale at 1:00 AM UTC (6:30 AM in INDIA) appears in the March 10 report.Ran the complete test suite successfully.

**Commits:** TODO: cd563c3 (HEAD -> main, origin/main) Fix daily sales report timezone boundaries

## 5. Report

> Ever since we started running two server workers, we have sold more units of an item than we had, and its stock went negative.

**Status:** TODO: Fixed 

**What I saw:** TODO: When the server workers handled stock reservations. Automatically, they could both reserve the same available stock, causing stock become negative.

**Root cause:** TODO: The reserve logic  checked stock using a per-process cache before updating the database different workers could read the same outdated stock value

**Fix and why:** TODO: Updated the stock reservation SQL to check "stock >= qty" and subtract the quantity in a single atomic database operation.This avoids reservations when insufficient stock remains.

**How I verified it:** TODO: I Added test_concurrent_stock_reservation, which uses two separate catalog instances with stale cached stock. It verifies that the second reservation fails and the remaining stock is correct.Run the complete test suite successfully

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
