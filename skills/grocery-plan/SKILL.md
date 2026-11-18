---
name: grocery-plan
description: Use when asked to plan a grocery shopping cycle from current ads, prices, coupons, and online options.
compatibility: Requires the notes skill with a grocery planning note, web search or fetch, and agent-browser for retailer pages that ordinary fetching cannot read.
disable-model-invocation: true
---

# Grocery plan

Plan one shopping cycle. Staples are bought every cycle, so find where to buy
them. Stock-up items are bought only on a good sale, so decide whether this
cycle's price is good enough. Return one plan; the user decides whether an extra
stop is worth it.

## Read the grocery note

Use [notes](../notes/SKILL.md) to find and read the grocery planning note and its
price log. Take every specific from them:

- Stores pinned to exact locations, their channels, loyalty programs, and fees.
- Staples, quantities, comparable units, and substitutes.
- Preferences.
- Stock-up items, grouped by category where loose, with comparable units,
  normal prices, baseline sources, buy-at prices, and quantities.

If the note is missing or a section the plan needs is empty, ask the user for
those specifics. Never invent stores, items, quantities, or prices. Take the
shopping date from the request; otherwise ask.

## Gather prices

For each store and channel in the note, check the weekly ad, regular shelf
prices, and digital coupons. Channels are in-store, pickup, delivery, and online.

- Select the exact store from the note before reading prices. A chain's default
  store or another ZIP code can show different prices.
- Check validity dates against the shopping date. Weekly ads often run Wednesday
  through Tuesday, so an ad can end before the trip, and the next ad may not be
  published yet. An offer that is not valid on the shopping date cannot set a
  recommendation or a total; report it as unverified.
- Use web search and fetch first. If they fail or return incomplete pricing, such
  as a page that needs a store selection or an ad with prices only in images,
  use [agent-browser](../agent-browser/SKILL.md).

For each price that changes the plan, keep its evidence: store and channel,
source link, validity dates, pack size, and conditions.

## Compare like with like

- Convert each price to the item's comparable unit, such as per pound, per
  ounce, or each. For items sold each but compared by weight, use the stated
  weight, or a typical weight labeled as an estimate.
- Price a multi-buy deal at what one unit actually costs, and check whether one
  unit gets the deal price. For a deal that requires buying N, price N units and
  say when N exceeds the planned quantity.
- Mark loyalty-only prices with the program they need.
- Apply per-household and per-transaction limits. Price units above a limit at
  the regular price.
- When a recommendation depends on a digital coupon the user may not have
  clipped, show whether it still wins without the coupon.

## Set the normal price

Judge each item against its own normal unit price. Use a normal price only when
it has a source, such as a shelf price, a receipt, or the price log, or when the
note labels it as a personal estimate. Say which in the plan. Without either,
report the current price and state that the item has no baseline.

Treat the price log as a source only when it includes ordinary prices. A history
of sales alone makes the normal price look lower than it is.

## Plan staples

Choose one primary store for the whole staple basket. List every staple there
with its price. When no price can be established, write "buy here; price
unverified": an item missing from a weekly ad says nothing about its shelf price.

Compare stores on complete basket totals. When unverified prices prevent that,
label each sum a verified subtotal, list the items it excludes, and call the
primary store provisional rather than cheapest. Never present the cheapest
partial basket as the winner.

Then list each extra stop or online order that saves money, and the items that
move. Claim a saving only when those items are priced at both stores, after
delivery and service fees. If the plan's items do not meet an order minimum or a
required quantity, label the order conditional and say what it would take.
Present overlapping options as alternatives; do not add their savings.

## Plan stock-up items

Recommend a stock-up item when its current unit price reaches the note's buy-at
price, in the quantity the note allows. Do not compare unit prices across
different items; a cheap item at its usual price is not a deal.

For a loose category, such as "protein" or "fruit", rank the acceptable items
that reach their buy-at prices by percentage below their own normal prices, then
apply preferences. Leave items without a baseline unranked. Preferences are soft:
say when a preferred item wins with a smaller discount.

Keep optional stock-up spending separate from the staple total.

## Return the plan

Give one plan in this order:

1. The primary store, every staple, and the staple total or verified subtotal.
2. Extra stops and online orders, with net savings.
3. Stock-up buys with their own total, then declined stock-up items with the
   price seen and the buy-at price.
4. Missing sources, stale ads, and other gaps.

Put the evidence beside each price that changes the plan.

## Log prices

Append dated observations to the price log for staples and stock-up items
compared this cycle. Record ordinary and declined prices, not only sales. Follow
the columns the log defines and save it through [notes](../notes/SKILL.md).

## Close the cycle

After the user shops, ask:

- What changed at checkout, such as prices, substitutions, out-of-stock items,
  or coupons that did not apply.
- Which steps took manual effort.

Log the prices the user paid. Cite the receipt when the user supplies one;
otherwise mark the source `user-reported`. Save the answers in the note's
checkout notes; they show what, if anything, to automate later.
