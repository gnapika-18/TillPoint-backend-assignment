# NOTES

Fill in every field below before you submit. We read this file first: a submission with an empty field is not graded.
Write in your own words. If you did not fix something, set Status to `not fixed` and say what you found and tried; that still counts.
Status is one of: `fixed`, `partly fixed`, `not fixed`. Commits: the short hashes of the commits for this item, or `none`.

## 1. Report

> Finance says that on some days the revenue in the daily report is higher than what the payment provider paid out, and a few orders show more money received than their total.

**Status:** : Fixed (after Duplicates webhook text cases Passed)

**What I saw:** : The same successful payment webhook could be received multiple times. Each delivery was inserted as a new payment record,which is increase the received amount for as order and make the daily revenue report higher than the actual amount received.

**Root cause:** : There is no UNIQUE for event_id so, the payment handler did not check whether a webhook event ID had already been processed. The database also allowed duplicate event ID's

**Fix and why:** : I added a check to see whether the event ID already exists before inserting a payment. If the payment is already proccessed, handler returns without inserting it again. I also added UNIQUE constraint to the event_id to prevent duplicate records at the database level.

**How I verified it:** : I added a new test named 'test_duplicate_payment_webhook'. It sends the same successful payment event twice and verifies that the payment amount counted only once. I ran the automated tests to check the Changes.

**Commits:** : 67ce727 (HEAD -> main, origin/main) Fix duplicate payment webhook processing with test case

## 2. Report

> Products show as sold out, or nearly, while the shelf is full. Staff say it gets worse on days when many customers abandon checkout.

**Status:** : Partly Fixed

**What I saw:** : Product Stock was reduced when an order was created, even before payment. When a pending order or cancelling the order was cancelled, the reserved stock was not returned. Abandoned orders  are keep stock unavailable.

**Root cause:** : Stock was reserved only for order creation,logic went wrong during cancellation logic restored for paid orders. Pending orders were ignored.

**Fix and why:** : I updated the cancellation logic to restore stock for both pending and paid orders. This can make that after unapid and order cancelled can stock restored.

**How I verified it:** : Added a new test named test_cancel_pending_order_restocks. It verifies that stock returns to its original quantity after cancelling a pending order. Ran the complete test suite, and all tests passed.

**Commits:** 8161e4c (HEAD -> main, origin/main) Restore stock when pending orders are cancelled

## 3. Report

> Customers in taxed regions who used a coupon say the tax on their receipt is too high. Receipts without a coupon are right.

**Status:** : Fixed 

**What I saw:** : Customers in taxed regions were charged too much tax when they used a coupon. Orders without coupouns had the correct tax.

**Root cause:** : Tax was calculated on the original subtotal before deducting the coupon discount.

**Fix and why:** : Updated price_order() to calculate the coupon discount first then calculate tax on the discounted subtotal.This is the  customers are charged tax on the correct amount.

**How I verified it:** : Added test_coupon_tax_calculated_after_discount that  checks the subtotal, discount,tax,and final total for a coupon order in California.Ran the complete test suite successfully.

**Commits:** : b86f3db (HEAD -> main, origin/main) Calculate tax after applying coupon discount

## 4. Report

> Our shop in India (UTC+5:30) says sales taken in the morning do not appear in that day's report and turn up under the day before.

**Status:** :Fixed 

**What I saw:** : Sales made during the morning in INDIA appeared under the previous day's report instead of this correct local date.

**Root cause:** : The Utc offset is added instead of subtracted when the calculating the UTC boundaries of a local calender day.

**Fix and why:** : Just i updated with exchanging of the sign + to - that means day_bounds_utc() to subtract the shop UTC offset when calculating the UTC start and end of the local day.This correctly Shows INDIA UTC+5:30 timezone.

**How I verified it:** :Added test_daily_sales_india_timezone, that can checks that a sale at 1:00 AM UTC (6:30 AM in INDIA) appears in the March 10 report.Ran the complete test suite successfully.

**Commits:** : cd563c3 (HEAD -> main, origin/main) Fix daily sales report timezone boundaries

## 5. Report

> Ever since we started running two server workers, we have sold more units of an item than we had, and its stock went negative.

**Status:** : Fixed 

**What I saw:** : When the server workers handled stock reservations. Automatically, they could both reserve the same available stock, causing stock become negative.

**Root cause:** : The reserve logic  checked stock using a per-process cache before updating the database different workers could read the same outdated stock value

**Fix and why:** : Updated the stock reservation SQL to check "stock >= qty" and subtract the quantity in a single atomic database operation.This avoids reservations when insufficient stock remains.

**How I verified it:** : I Added test_concurrent_stock_reservation, which uses two separate catalog instances with stale cached stock. It verifies that the second reservation fails and the remaining stock is correct.Run the complete test suite successfully

**Commits:** : 0104775 (HEAD -> main, origin/main) Prevent overselling with atomic stock reservation

## 6. Open question

> Open question from support, no single right answer: sometimes a customer really does pay twice for one order (two separate payments, not a retry of the same one). Today the system records both and says nothing. Decide what should happen, build the smallest version of your decision, and explain what it costs in `NOTES.md`.

**My decision:** :An order should not accept payments beyond its total amount. Once fully paid, any separate successful payment for that order is rejected.

**Why:** :This avoids duplicate payments from being counted as additional revenue for the same order

**What it costs:** : If a customer pays twice, the second payment is rejected by our system, but the money already collected so, we need refund or reconciliation process would be needed.

**What I built:** : Added a remaining-balance check before recording a payment. Partial payments are accepted,payments completing the order are accepted,and payments exceeding the remaining balance are rejected. Existing duplicate webhook event protection is preserved

**How I verified it:** :  Added test_separate_second_payment_rejected to verify that a second separate payment after full payment is rejected and the recorded payment total remains unchanged. Successful

**Commits:** : 614656e (HEAD -> main, origin/main) Reject payments exceeding order balance

## Other things I noticed

Problems outside the reports: what, where, and whether you fixed them. Write `none` if there were none.

TODO: A separate refund or payment reconciliation process is needed when a customer is charged twice for the same order. The current implementation rejects the second payment from being recorded in the system, but it does not automatically refund money already collected by the payment provider. This remains an improvement for future implementation.

## Session logs

Which AI tools you used, and one line per file in `session_logs/`.

TODO: fill in
