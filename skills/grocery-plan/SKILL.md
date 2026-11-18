---
name: grocery-plan
description: Use when asked to plan a grocery shopping cycle from current ads, prices, coupons, and online options.
compatibility: Requires the notes skill with a grocery planning note, web search or fetch, and agent-browser for retailer pages that ordinary fetching cannot read.
disable-model-invocation: true
---

# Grocery plan

Read the grocery planning note and its price log through [notes](../notes/SKILL.md).
They hold every personal specific: stores, staples, preferences, stock-up
targets, and normal prices. Ask for anything missing; never invent it.

- Check prices at the exact store locations in the note, since prices vary by
  location. Use only offers valid on the shopping date.
- **Staples:** Choose one primary store and list every staple there. List an
  item whose price you cannot verify as "buy here; price unverified"; absence
  from a weekly ad says nothing about its shelf price. Never present the cheapest
  partial basket as the winner. Show what each extra stop or online order saves
  after fees and minimums, and let the user decide.
- **Stock-up items:** Judge each item against its own normal unit price, not
  against other items, and recommend it only at or below its buy-at price. In a
  loose category, such as "protein", pick the item furthest below its own normal
  price, then apply preferences. Total stock-up spending apart from staples.
- Use a normal price only when it has a source or the note labels it a personal
  estimate.
- Beside each price that changes the plan, give its store, channel, source link,
  validity dates, and conditions. If it depends on a coupon the user may not
  have clipped, say whether it still wins without the coupon.
- Log every compared price, including ordinary and declined prices. A log of
  bargains alone teaches a baseline that is too low.
- After the user shops, ask what changed at checkout and which steps took manual
  effort. Record both in the note.
