# sticky residential proxies: how sticky sessions work, how long an IP really lasts, and how to pick a plan that doesn't waste bandwidth

You opened a scraper, logged into a site with a real account, walked away for two minutes, and came back to a password prompt and a flagged session. Or your cart emptied itself during a checkout test. That's usually not a website being paranoid — it's a rotating proxy handing you a different exit IP every few requests, and the site noticing that the "same user" just teleported from Ohio to Vietnam.

Sticky residential proxies exist to fix exactly that. The question most people actually have isn't "what is a sticky proxy" — that part takes one sentence. It's how sticky you can get, how long the pin actually holds, what happens when the household device behind the IP goes to sleep, and how much that persistence costs compared to plain rotating traffic. Below is the practical version, using 9Proxy's current products and pricing as the concrete example, since their session controls are documented well enough to show the real mechanics.

## What "sticky" actually means in a residential proxy

A residential proxy network routes your request through a real household device. By default, most networks assign a new exit IP per request or per connection. Sticky mode does one thing: it pins your session to a single exit IP for a defined window, and anything else you send with the same session identifier goes out through that same IP until the window closes.

The window is usually controlled by two things in the proxy username or credentials:

- a session duration parameter (how many minutes the IP stays fixed)
- a session ID (a label that tells the network which pool of pinned IPs you belong to)

What sticky mode does **not** mean is "you now own this IP forever." You're borrowing a slot on someone's home internet connection. If their router reboots or the device drops offline, the IP can disappear before your timer runs out. Anyone selling you sticky residential IPs as a permanent fixed address is describing static ISP proxies, which is a different product built on datacenter-hosted infrastructure rather than live household devices.

## Sticky vs rotating: pick by the workflow, not by preference

Rotating wins when each request is independent and you want maximum IP diversity. Sticky wins when a site needs to believe one consistent human is doing a sequence of things. That's the whole decision.

| Workflow | Session mode | Why |
| --- | --- | --- |
| Pulling 10,000 independent product pages | Rotating | Each request stands alone; diversity spreads the load |
| Logging into an account and paging through a dashboard | Sticky | Cookies, tokens and IP need to stay consistent |
| Checkout or cart-flow testing | Sticky | A mid-flow IP change reads as session hijacking |
| Localized SERP checks from one city | Sticky, short TTL | Long enough to finish the query set, short enough to avoid buildup |
| Ad and landing-page verification across regions | Either | Rotating for breadth, sticky when the ad flow is multi-step |
| Automated account management at scale (authorized) | Sticky with `ssid` per account | One stable identity per account, no cross-contamination |

If you can't decide, the answer is usually: sticky for anything with a login, a cart, or a multi-step funnel; rotating for anything you'd be happy to run curl against in a loop.

## How long can a sticky residential session last?

This is where marketing pages get vague and provider documentation gets honest. Three limits stack on top of each other:

1. **Your configured session time.** You choose it. In 9Proxy's GB-based system you pass `sst` in the username (minutes), and the IP holds for that long before rotating.
2. **The natural uptime of a residential IP.** Real household connections are not always-on servers. Documented behaviour for 9Proxy's IP-based product is a few hours up to roughly 24 hours depending on the individual IP. A residential device can go offline well before that.
3. **Network policy.** Some providers cap sessions at 10, 30, or 1440 minutes regardless of what you request.

Practical takeaway: treat a long sticky session as a best-effort estimate, not a contract. If your workflow needs a genuinely fixed address for weeks, sticky residential is the wrong tool — look at static ISP proxies instead. If your workflow needs a stable identity for the duration of a login-and-navigate sequence, sticky residential is exactly right.

## Setting up sticky sessions on 9Proxy

9Proxy runs two residential products and only one of them exposes session duration as a configurable parameter, which is the single most important thing to understand before you buy.

**Residential Proxy by GB** is the flexible one. You buy bandwidth, generate as many endpoints as you want, and control targeting and session behaviour through a structured username. The documented format is:


<sub-user>-country-<country_code>-st-<state_code>-city-<city_name>-isp-<isp_code>-sst-<session_time>-ssid-<session_id>


Sticky examples straight from the docs:


subaccount-country-us-sst-15
subaccount-country-de-city-berlin-sst-30
subaccount-country-us-sst-15-ssid-id1
subaccount-country-us-sst-15-ssid-id2


That last pair matters more than it looks. If you run five parallel workers and give them all the same username with no `ssid`, they fight over one pinned IP. Give each worker its own `ssid` and each gets a distinct sticky IP from the same configuration. This is the difference between a working multi-account setup and five accounts sharing one address.

Authentication works two ways on this product: username and password (with sub-users you assign traffic to), or IP whitelisting, where a whitelisted device connects with no credentials at all. Targeting covers country, state, city, and ISP.

**Residential Proxy by IPs** works differently. You buy a block of IPs, pay per IP with unlimited bandwidth, and forward traffic through them. There's no `sst` parameter because you're holding the address rather than pinning a session onto a shared pool. Each IP stays live for a few hours up to about a day, unused IPs don't expire, and there's an Auto Rotation Proxy if you want to cycle ports on your own schedule. If your mental model is "I need N persistent identities," this is the product. If it's "I need one IP for exactly 20 minutes per job," the GB product is cheaper and more precise.

Both run over HTTP/HTTPS and SOCKS5, across 20M+ residential IPs in 90+ countries.

👉 [Start with 9Proxy and generate your first sticky endpoint](https://bit.ly/9-Proxy)

## Which 9Proxy plan actually makes sense for sticky work

The pricing here needs a caveat: 9Proxy announced a price adjustment effective June 1, 2026 that raised IP-based and bundle pricing, while leaving GB-based pricing untouched. Older reviews floating around still quote the pre-adjustment numbers, which is why you'll see the same 100-IP package described as $20 and $24 in different places. The figures below reflect the post-adjustment structure.

| Plan | What you get | Price | Effective rate | Best for sticky use |
| --- | --- | --- | --- | --- |
| GB — 5 GB | 5 GB traffic, 180-day validity | $15 | $3.00/GB | Testing sticky configs before committing |
| GB — 50 + 5 GB | 55 GB traffic, 180-day validity | $105 | $2.10/GB | Light session-based scraping, a few targets |
| GB — 100 GB | 100 GB, 180-day validity | $150 | $1.50/GB | Regular automation with login flows |
| GB — 200 GB | 200 GB, 180-day validity | $200 | $1.00/GB | Medium monitoring jobs across sessions |
| GB — 1,000 GB | 1,000 GB, 180-day validity | $800 | $0.80/GB | Daily session work at scale |
| GB — 2,000 GB | 2,000 GB, 180-day validity | $1,500 | $0.75/GB | Multi-tool teams, several regions |
| GB — 10,000 GB (Enterprise) | 10,000 GB, unlimited validity, team mode up to 5 members | $6,800 | $0.68/GB | Agencies running always-on sticky pipelines |
| IPs — 100 IPs | 100 residential IPs, unlimited bandwidth, no expiry | $24 | $0.24/IP | Small fixed-identity projects |
| IPs — 500 IPs | 500 residential IPs, unlimited bandwidth | $72 | $0.144/IP | Solo operators, light multi-account work |
| IPs — 1,000 + 500 bonus | 1,500 residential IPs, unlimited bandwidth | $126 | $0.084/IP | Small teams with steady workloads |
| IPs — 2,500 IPs | 2,500 residential IPs, unlimited bandwidth | $210 | $0.084/IP | Several verticals in parallel |
| IPs — 5,000 IPs | 5,000 residential IPs, unlimited bandwidth | $360 | $0.072/IP | Agencies, mid-scale SEO and price stacks |
| IPs — 15,000 IPs | 15,000 residential IPs, unlimited bandwidth | $720 | $0.048/IP | Larger businesses, regional teams |
| IPs — 25,000 IPs | 25,000 residential IPs, unlimited bandwidth | $863 | $0.035/IP | Resellers, heavy automation labs |
| IPs — 50,000 IPs | 50,000 residential IPs, unlimited bandwidth | $1,438 | $0.029/IP | High-volume resellers |
| Business IPs — 100,000 | 100,000 residential IPs, unlimited bandwidth | $2,300 | $0.023/IP | Platform-level operations |
| Business IPs — 200,000 | 200,000 residential IPs, unlimited bandwidth | $4,140 | $0.021/IP | Industrial-scale collection |
| Business IPs — 500,000 | 500,000 residential IPs, unlimited bandwidth | $8,625 | $0.018/IP | Reseller inventory at wholesale rates |
| Starter Bundle | 100 IPs + 5 GB, 180-day traffic validity | $30 | — | One project packing both models |
| Popular Bundle | 1,500 IPs + 50 GB, 180-day traffic validity | $180 | — | Teams mixing sticky and rotating workloads |
| Pro Bundle | 5,000 IPs + 500 GB, 180-day traffic validity | $720 | — | All-in packs for client work |

👉 [Check the current 9Proxy pricing and confirm these rates before buying](https://bit.ly/9-Proxy)

The short version on picking: **sticky sessions you configure per job → GB plans. Persistent identity pools → IP plans.** The 5 GB pack at $15 is the honest starting point if you just want to verify that `sst` and `ssid` behave the way you expect. You'll burn through 5 GB faster than you think if you're pulling heavy pages, but it's enough to prove session continuity on your actual targets.

One more cost consideration: 9Proxy offers an additional 5% discount or 5% product bonus with selected payment methods. Worth checking at checkout, easy to miss.

## Where sticky sessions earn their keep

**Account-based workflows you're authorized to run.** One identity, one IP, consistent across a session. This is the classic use case and the one where a mid-session IP switch causes the most damage.

**Checkout and funnel testing.** Payment flows are extremely sensitive to session changes. A cart that empties itself mid-test tells you nothing about the checkout under test.

**Session-scoped data collection.** Sometimes a site only reveals the full dataset if you page through it as one visitor. Rotating IPs mid-pagination either breaks the sequence or trips a rate limit tied to your session.

**Localized SERP and geo checks.** Hold an IP in the target city for the length of a query set, then let it rotate. Short sticky windows give you cleaner comparable results than switching exit nodes between queries.

**Ad verification.** Multi-step ad journeys — click, land, convert path — need one consistent IP to produce a trustworthy result.

## The things that actually break sticky sessions

**Forgetting `ssid` on parallel workers.** Five concurrent jobs, one pinned IP, five sets of cookies colliding. This is the single most common mistake with username-parameter session models.

**Assuming the IP will outlive your timer.** Real household connections drop. Build retry logic that detects an IP change mid-session and restarts the sequence rather than continuing with corrupted state.

**Using sticky mode for work that wants diversity.** Sticky sessions concentrate traffic on one address. On a target that rate-limits per IP, that's the fastest way to get throttled.

**Holding sessions open for no reason.** On a bandwidth-billed plan, idle sessions still cost you because the requests you do send consume the balance. Don't set `sst` to 60 minutes if the job takes 90 seconds.

**Expecting enterprise-grade success on hard targets.** Independent comparison work places 9Proxy in the budget residential tier, around $1.30–$2/GB at standard volumes, with reported Tier 1 success rates of 95%+ and Tier 2 in the 85–92% range. Heavily protected properties and high-volume SERP work sit outside what a budget pool reliably delivers, and city-level pool depth is thinner than mid-market providers offer. Sticky sessions fix identity consistency. They do not fix a small IP pool.

## FAQ

**Is a sticky residential proxy the same as a static residential proxy?**
No, though the terms get mixed up constantly. Static residential (often sold as ISP proxies) means a fixed IP hosted on datacenter infrastructure that stays assigned to you. Sticky residential means a rotating-pool IP pinned for a session window. Sticky is cheaper and more authentic in origin; static is genuinely persistent.

**How long is too long for a sticky session?**
Long enough to complete the workflow, plus a small buffer. If your job needs 8 minutes, a 15-minute `sst` is sensible. A 60-minute session on a residential IP is a request, not a guarantee.

**Does sticky mode use more bandwidth than rotating?**
Same cost per request in theory, but sticky sessions can consume more in practice — you're more likely to load full page assets to keep a session alive, and long sessions invite retries. Measuring actual GB per completed task is the only number that matters.

**Can I run several sticky IPs at once from one configuration?**
Yes, in 9Proxy's GB product: each unique `ssid` produces a different pinned IP even with identical country and `sst` values.

## The bottom line

Sticky residential proxies solve a specific problem: keeping one believable identity through a multi-step workflow. They don't make a cheap pool behave like an enterprise network, and they don't replace static IPs when you need something genuinely permanent. If you know your sessions need to last minutes rather than weeks, configure `sst` and `ssid` properly, start small on a bandwidth plan, and measure success per completed task instead of per request.

👉 [Open a 9Proxy account with the invite link and test sticky sessions on your own targets](https://bit.ly/9-Proxy)
