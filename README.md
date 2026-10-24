# rotating proxies cheap: what the per-page math really looks like, and when a credit-based API beats a per-GB proxy pool

Most "rotating proxies cheap" listings are advertising a number that has very little to do with what you'll actually pay. Rotation itself is a solved problem — the price you end up with depends on how a provider meters you: per request, per credit with hidden multipliers, or per GB of bandwidth.

This article breaks that math down with ScraperAPI as the main example. It's one of the best-known rotating-proxy services, but it doesn't sell GB — it sells API credits, and a single request can cost anywhere from 1 to 75 of those credits depending on the site you're hitting. Whether that makes it cheap or expensive for you is exactly the calculation below. If you'd rather plug raw IPs into your own stack, we'll cover that case too.

## Quick picks before the details

The short version, based on ScraperAPI's current official pricing:

> **Writing scraping code and tired of getting blocked?** ScraperAPI handles proxy rotation, CAPTAs, JS rendering and retries through one endpoint. On plain HTML pages, its Business plan works out to about **$0.10 per 1,000 pages**. Start with the free trial — 7 days, 5,000 credits, no credit card.
>
> 👉 [Start the free ScraperAPI trial — 5,000 credits, no card needed](https://www.scraperapi.com/pricing/?fp_ref=coupons)
>
> **Just need IPs for your own browser stack?** A per-GB rotating pool is usually cheaper for JavaScript-heavy work. Evomi advertises rotating residential proxies from $0.49/GB with a free trial; DataImpulse from $1/GB.

## Two pricing models, two very different bills

Rotating proxies come in two billing shapes, and mixing them up is the fastest way to overspend.

Per-GB providers charge by bandwidth. Pull 10 GB in a month at $0.49–$1/GB and your bill is $5–$10, regardless of how many requests those GB contained. You manage the rotation logic, the browser, and the CAPTCHA solving yourself.

ScraperAPI meters by request instead. Every request consumes API credits, and bandwidth is unlimited on every plan — you pay per page fetched, not per byte transferred. That structure makes sense when your pages are small and numerous. It stops making sense when your pages are heavy, or when the target site forces the expensive request types we'll get to in a minute.

## ScraperAPI's full plan lineup

Here's the complete current lineup, verified against the official pricing page. All paid plans carry a 10% discount on annual billing.

| Plan | Credits / month | Threads | Price (monthly) | Annual (per month) | Buy |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | $0 | — | — |
| Hobby | 100,000 | 20 | $49 | $44.10 | [Sign up for Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | $149 | $134.10 | [Sign up for Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | 3,000,000 | 100 | $299 | $269.10 | [Sign up for Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | $475 | $427.50 | [Sign up for Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | $975 | $877.50 | [Sign up for Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | $1,975 | $1,777.50 | [Sign up for Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Custom | Custom | — |

Every paid plan includes the same feature set: JS rendering, premium residential and mobile IPs, CAPTCHA and anti-bot bypass, JSON auto-parsing, custom sessions, automatic retries, unlimited bandwidth, and a 99.9% uptime guarantee. Higher tiers don't unlock new features; they buy more volume.

What actually differs between tiers, besides price:

- **Geotargeting.** Hobby and Startup can only target the US and EU. Country-level targeting across 50+ countries starts at Business ($299/month). If you need IPs from specific countries outside those two regions, the two cheap tiers won't do it.
- **Analytics retention.** Hobby and Startup only show the last 30 days in the dashboard; Business and up have unlimited history.
- **Billing flexibility.** See the section on running out of credits below — this is where the tiers genuinely diverge.

There's a permanent free plan too: 1,000 credits a month, 5 concurrent connections, no geotargeting. Enough to test, not to run anything.

## The credit system: 1 credit is not always 1 request

This is the part that decides whether ScraperAPI is cheap for you, and it's also the part most listicle reviews skip.

A standard page through the default pool costs 1 credit. Then the multipliers kick in, applied automatically based on the target domain and the options you enable:

| Request type | Credits |
| --- | --- |
| Standard page | 1 |
| Amazon (any page) | 5 |
| Google / Bing (incl. subdomains) | 25 |
| LinkedIn | 30 |
| `render=true` (JS rendering) | +10 |
| `premium=true` | +10 |
| `ultra_premium=true` | +30 |
| Cloudflare / DataDome / PerimeterX bypass | +10 (auto-detected) |

Two details worth knowing before you budget anything:

**The multipliers stack non-linearly.** Combining `premium=true` with `render=true` costs 25 credits, not 20. Ultra-premium plus rendering costs 75, not 40. Protected targets with JavaScript are the most common real-world scraping scenario, and they burn credits roughly three times faster than adding the individual surcharges would suggest.

**You can price a URL before scraping it.** The dashboard includes a Domain Cost Estimator that shows the exact per-request cost for any URL, and you can set a `max_cost` parameter so a single scrape can never exceed a credit limit you define. Use both. Ten minutes of checking beats a month of discovering your credits vanished in week one.

For context, here's what the multipliers do to real per-page costs, calculated on the Business plan:

| Workload | Cost per 1,000 pages |
| --- | --- |
| Plain HTML | $0.10 |
| E-commerce (Amazon-class) | $0.50 |
| JS-rendered pages | $1.00 |
| Google / Bing SERP | ~$2.49 |
| Ultra-premium + JS | ~$7.48 |

On the $49 Hobby plan, that same ultra-premium-plus-JS workload comes to about **$36.75 per 1,000 pages** — and 100,000 credits translates to roughly 1,333 actual requests. At that point "cheap rotating proxies" is doing a lot of work in the sentence.

One caution if you explore the product beyond the core API: DataPipeline, the no-code scheduled-scraper feature, bills on its own credit table at higher rates — a basic normal request costs 6 credits there versus 1 on the standard API. The rates are documented, but easy to miss.

## What happens when your credits run out

ScraperAPI credits expire at the end of each billing cycle. They don't roll over, and there's no banking unused volume toward next month.

When you hit zero, the default behavior is that scraping pauses until renewal. Your options from there depend on your tier:

1. **Manual upgrade** — available everywhere, usually at a better per-credit rate than your current plan.
2. **Auto-upgrade** — opt-in, available on Hobby through Professional; moves you to the next plan automatically when credits run out.
3. **Pay-as-you-go** — available only on Scaling ($475/month) and above; keeps scraping at a fixed per-credit rate up to a monthly spending cap you set.

That last row is the trap for people picking the smallest plan to "stay cheap." On Hobby, Startup, or Business, a mid-month spike doesn't just cost you overage — it stops your pipeline dead until you act or the cycle resets. If your monthly volume is unpredictable, either size up a tier or enable auto-upgrade deliberately rather than discovering the cutoff during a production run.

One related quirk: hitting your plan's concurrent-thread limit doesn't cost anything. Over-limit requests come back with a 429 and are turned away rather than queued, so you retry them yourself later.

## When ScraperAPI is the cheap option — and when it genuinely isn't

ScraperAPI wins where most people searching for cheap rotating proxies actually are: scraping standard e-commerce and SEO targets at moderate volume, without writing and maintaining your own rotation and retry logic. Reviewers consistently flag the setup speed (register, get an API key, first request in minutes) and note that for targets without heavy bot protection, the default pool clears most requests without ever touching premium tiers. The structured-data endpoints — which return parsed JSON for Amazon, Walmart, Google Search and Shopping instead of raw HTML — save real parsing code if you're feeding the output into dashboards or price monitors.

Several parameters also cost nothing extra: `country_code`, `session_number`, `device_type`, and `autoparse` all bill at the base rate. Sticky sessions are time-limited (short enough that they suit a single scraping run rather than multi-day IP consistency), so account-based work isn't a good fit.

The math flips when your workload is dominated by JS-heavy or heavily protected pages. At 10–75 credits per request, doing the rendering and CAPTCHA work yourself against a per-GB pool comes out ahead at scale. Concretely: if your JS-heavy workload needs about 10 GB of traffic a month, a pool at Evomi's advertised $0.49/GB runs roughly $5 monthly, and DataImpulse's $1/GB tier runs about $10 — versus $49 for ScraperAPI's Hobby plan, which at 10-credit rendering buys you about 10,000 rendered requests. For low-but-real volumes, ScraperAPI's convenience is often worth the difference; for anything past a few tens of thousands of rendered pages a month, run your own numbers before assuming that.

## Three checks before you pay

1. **Estimate your real per-page cost first.** Put your actual target URLs into the Domain Cost Estimator, note the credit cost per request, and multiply by your monthly page count. Compare that against the plan tiers. If the cost lands above your current tier's capacity, the bigger tier is usually cheaper per credit anyway.
2. **Use the free trial on a real job.** Seven days and 5,000 credits with no card required is enough to run an actual workload against your actual targets and see the true burn rate — not the pricing page's best case.
3. **Decide what a mid-month cutoff would cost you.** If your scraper stopping for three days is a real problem, pick a Scaling-or-above tier with pay-as-you-go, or turn on auto-upgrade before you need it.

👉 [Check current plan prices and run the trial](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## FAQ

**Do ScraperAPI credits roll over to next month?** No. Credits expire at the end of each billing cycle, on every plan including annual billing. If you finish a year prepaying, unused credits from earlier cycles are gone.

**Is the free plan usable for a real project?** Not really. It's 1,000 credits a month with 5 concurrent connections — fine for validating that your parsing works, not for ongoing volume. The 7-day, 5,000-credit trial is the more meaningful test window.

**Can I target countries outside the US and EU on the cheap plans?** No. Hobby and Startup are US & EU only. Country-level targeting across 50+ countries requires Business at $299/month or higher.

**What does a typical "1 million credits" plan actually buy?** Depends entirely on your targets. Plain HTML: about a million pages. Amazon with JS rendering: perhaps 40,000–100,000 pages, based on reviewer calculations of the multipliers. This is why the per-URL cost check matters more than the credit count.

## Picking, by situation

- A few thousand simple requests a month, or still evaluating → the free plan, then Hobby at $49 if you outgrow it. Don't start higher.
- Price monitoring or SERP tracking against standard e-commerce sites, roughly 20,000–200,000 pages monthly → Business at $299 (or $269.10 on annual billing) is the practical sweet spot; it's also where country-level geotargeting begins.
- Mostly JS-heavy or Cloudflare-protected targets → do the per-page math honestly. A per-GB pool from a vendor like Evomi (from $0.49/GB) or DataImpulse (from $1/GB) will very likely cost less, at the price of building your own retry and CAPTCHA handling.
- Multi-million requests a month → Scaling at $475 and up, where pay-as-you-go and better per-credit rates start.

👉 [View ScraperAPI's full plan list and start free](https://www.scraperapi.com/pricing/?fp_ref=coupons)

Last thing before checkout: verify the credit multiplier for *your* domains in the dashboard estimator rather than trusting examples anywhere — including this article. That one number is the difference between "cheap" and a surprise invoice.
