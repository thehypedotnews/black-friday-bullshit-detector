# Bright Data tools

Checked against the `brightdata/brightdata-mcp` README, CHANGELOG and docs.brightdata.com on 25 Nov 2026, and against 18 live test runs the same day. The server is updated often, so trust the tool list you actually see over this file. Tool names may carry a connector prefix.

## Which tool for which step

| Step | Tool | Notes |
|---|---|---|
| Read one page | `scrape_as_markdown` (being renamed `web_fetch`) | `url`; `format`: `markdown` (default), `text`, `html` or `ld_json`. No JavaScript rendering. Stops at 45 s. Pages over 40,000 characters are cut: call again with the `snapshot_id` and `start` from the result's `next` to read on, at no extra cost. Read-only, so several calls in one turn run at the same time (seen in Claude Code). |
| Read up to 5 pages | `scrape_batch` | 40,000 characters in total, shared between the pages; each cut page has its own `next`. Stops at 35 s. In tests 10 of 58 pages timed out inside a batch (Best Buy 5 of 9, Walmart 4 of 17), while all 30 single fetches worked; prefer one `scrape_as_markdown` per page. |
| Search | `search_engine` | `query`, `engine` (`google` or `bing`, Bing by default), `country`, `page` (from 0), `time_range`. 10 results per page. Stops at 55 s. |
| Up to 5 searches | `search_engine_batch` | `queries`: a list of the same objects. One result or error per query; the call waits for the slowest. In tests, Google batches answered in 2–4 s and Bing batches mostly in 40–55 s, often with a timeout. |
| Structured store data | `run_scraper` → `get_scraper_results` | Clean JSON per product. Not read-only: Claude runs it alone, and it waits up to 45 s before returning a `snapshot_id` with status `running`. `get_scraper_results` returns `running` until the data is ready, so `get_scraper_progress` adds nothing. Use `search_scrapers` and `get_scraper_details` only for a scraper SKILL.md and `stores.md` don't cover. |
| Pages that need clicks or JavaScript | `scraping_browser_navigate`, `scraping_browser_get_text`, and other `scraping_browser_*` tools | Last resort, untested here: costs 5 credits per MB and is slower. Navigate and read only; never click buy, sign-in or form buttons. |
| Usage so far | `session_stats` | Tool calls made in this session. |

`ld_json` returns the page's schema.org product data when it has some (Best Buy, apple.com, many maker stores). Amazon has none, so you get the markdown again; Walmart's holds only a page stub. Markdown carries the "was" price, seller and badges, so it's the one format to use for store pages.

Search results can hold redirect links. Bing's `/rebates/welcome?url=<encoded store URL>` carries the real address in `url=`: decode it. Google's `/goto?url=...` links are ads; skip them. `site:` queries failed or timed out on Bing in tests; write the store's name as a plain word instead.

### Older names you may meet

- `scrape_as_markdown` / `web_fetch`: the same fetch tool. On 25 Nov 2026 the hosted server still offered `scrape_as_markdown`; the rename to `web_fetch` was listed as unreleased in Bright Data's changelog.
- `scrape_as_html` and `extract`: removed on 5 Nov 2026. Use the fetch tool with `format: "html"`.
- `web_data_*` tools (store tools such as `web_data_amazon_product` or `web_data_walmart_product`): removed on 11 Oct 2026 in favour of the scraper tools. If a server still offers them, they work like a structured scraper for that store.
- "Rapid" and "Pro" modes: gone since 15 Oct 2026. All tools are on by default unless the connector URL limits them with `&groups=` or `&tools=`.

## Cost

Bright Data's free tier is 5,000 credits a month per account, renewing on the 1st, with no roll-over. A search query or a page read costs 1 credit, a scraper record costs 1 credit, and the remote browser costs 5 credits per MB. The test checks used about 9–16 credits each (counting every query, page and scraper input), so the free tier covers about 300 checks a month. Keep scraper `limit_per_input` at 3 if you ever use a `discover_by_*` method.

## Errors and what to tell the shopper

Errors look like `<Title>: <what happened>. <what to do> Details: ...`. Go by the title.

| Title | Meaning | Tell the shopper |
|---|---|---|
| Sign-in failed | Bad or revoked API token | "Bright Data didn't accept the token. Copy it again from brightdata.com/cp/setting/users and update the connector URL." |
| Usage limit reached | Free credits or funds used up, or a zone limit hit | "Your Bright Data credits are used up for now. The free ones renew on the 1st, or you can add funds in Bright Data's billing page." |
| IP not allowed | The token only allows certain IPs | "Your Bright Data token is locked to certain IP addresses. Remove that limit in Bright Data or make a new token." |
| Rate limited / Call limit reached | Too many calls too fast | Wait a few seconds and retry once; then carry on without that store. |
| Site not found / page_not_found | Dead or mistyped link | Ask for the link again. |
| Search failed / Search timed out | One search query failed | Retry that query once, reworded or on the other engine. |
| Page timed out, Collection failed, Browser timed out | The store page was slow or blocked | Fetch it once more on its own. Then list the store under "Couldn't check". |
| Scraper not found / Scraper input rejected | Stale scraper ID or wrong input shape | `search_scrapers` again, or `get_scraper_details` and match its `sample_input`. |

Never paste the token, the full connector URL or raw error details into the answer.
