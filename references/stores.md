# Store tips

More detail per store, and the scrapers for stores beyond the big four. SKILL.md already has what every check needs (where seller, "was" label, membership and model sit on Amazon, Walmart, Best Buy and Target; the four big-store scrapers). Read this file when you go beyond that. Page layouts change often; if a field isn't where this says, read the whole page before giving up. Labels below are examples, not a promise.

## How each big store behaved in live tests (25 Nov 2026)

| Store | Page fetch (`scrape_as_markdown`) | Scraper by URL | Notes |
|---|---|---|---|
| Amazon | works, 1–24 s | 10–30 s, usually ready in one call | `ld_json` returns the same markdown. Rewrite `/clp/<ASIN>` links to `/dp/<ASIN>`. |
| Walmart | works, 4–26 s | 23 s once, 90–120 s or never otherwise | `ld_json` holds only a page stub. Pages sometimes come back in Spanish. `discover_by_keyword` needs a `domain` field and returned nothing in 2 of 2 tries. |
| Best Buy | works alone, 5–28 s; inside `scrape_batch` it timed out 5 of 9 times | 100+ s or never | `ld_json` has a Product record with price, but markdown has the "was" price too, so one markdown fetch is enough. |
| Target | always times out (45 s) | 90–250 s when it finished at all (3 of 19) | `discover_by_keywords` and `discover_by_upc` never finished. |

`run_scraper` is not a read-only tool, so Claude runs it on its own: it holds up the other calls in its turn while it waits up to 45 s. A job still running after that returns a `snapshot_id`; read it later with `get_scraper_results`. Each returned record costs one credit.

## Scrapers for other stores and makers

From Bright Data's scraper catalog (`docs.brightdata.com/scrapers.json`) on 25 Nov 2026. Only the big-four ones were tried in tests, so the others' speed is unknown: expect 45 s or more. IDs can change: if `run_scraper` says "Scraper not found", look the store up with `search_scrapers`. Inputs: `collect_by_url` takes `[{"url": "<product URL>"}]`; for anything else, call `get_scraper_details` once for the exact input shape.

| Store | dataset_id | Methods |
|---|---|---|
| Costco | `gd_mkcbmac44j178pook` | collect_by_url |
| Sam's Club | `gd_mljead55j96getj3h` | collect_by_url |
| B&H | `gd_mkce0sox1mchrlpp8g` | collect_by_url |
| Newegg | `gd_mkcnpcq825jb5uiuna` | collect_by_url |
| Micro Center | `gd_mkckexq2uquhupguv` | collect_by_url |
| GameStop | `gd_mktp5v432bmlul6bvd` | collect_by_url |
| Home Depot | `gd_lmusivh019i7g97q2n` | collect_by_url, discover_by_keyword, discover_by_upc |
| Lowe's | `gd_lnvl79pfftqh18u2o` | collect_by_url, discover_by_keywords |
| Kohl's | `gd_mjwhpd7x2m0n36whj8` | collect_by_url, discover_by_keyword |
| Staples | `gd_mkpali8c17mjkucviv` | collect_by_url |
| Apple | `gd_ml87ng90wjb9sc1bi` | collect_by_url |
| Samsung | `gd_mljeeiwk1i1pmpp4nw` | collect_by_url |
| Sony | `gd_mm7u6vlo1gek8d7oe9` | collect_by_url |
| Bose | `gd_molhmkvp1xbzix13cz` | collect_by_url |
| LG | `gd_mm27jcwxgfzw6lywc` | collect_by_url |
| Dell | `gd_mkxzn676dd0x7mbcz` | collect_by_url |
| HP | `gd_ml87x26128yz24a85s` | collect_by_url |
| Lenovo | `gd_mlf1fywqjstgnzsxc` | collect_by_url |
| Microsoft | `gd_molhdfmhm86pwm2j9` | collect_by_url |
| Nintendo | `gd_mlf3994e2d5kpnxpkr` | collect_by_url |
| LEGO | `gd_leenwt162rg85apy87` | collect_by_url |
| SharkNinja | `gd_mljejilb2k0zfl1e79` | collect_by_url |
| Dyson | `gd_mosh8nch2o5nystic7` | collect_by_url |
| KitchenAid | `gd_molhizfr1ap9iimr8g` | collect_by_url |
| Google Shopping | `gd_ltppk50q18kdw67omz` | collect_by_url, discover_by_keyword (leads only, never evidence) |

Use a maker or extra-store scraper only when that store's page fetch came back without a price (pages built by JavaScript, such as nintendo.com in tests). If a run fails, record "couldn't read".

## Amazon (amazon.com)

- **URL:** `/dp/<ASIN>` or `/gp/product/<ASIN>`. The ASIN is Amazon-only. The one exception: other stores sometimes print it as the model number of Amazon's own devices (Echo, Kindle, Fire TV).
- **Price:** the main buy-box price. Labels near it may include "List Price", "Typical price" or "Was Price". Report the label exactly as shown; don't explain what Amazon means by it.
- **Seller:** "Ships from" and "Sold by". "Amazon.com" or the brand's official store counts; anyone else is a marketplace seller. The buy box can switch to a third-party seller when Amazon is out of stock. The scraper's `buybox_seller` says who holds it.
- **Membership:** "Prime exclusive deal" or "Prime member price" is a paid-membership price, so it goes in the caveats. A "clip coupon" checkbox is open to anyone: count the price after the coupon and say "with on-page coupon". Store-card sign-up discounts don't count.
- **Condition:** "Renewed", "Used" or "Amazon Resale" (formerly Warehouse) means not new.
- **IDs:** the model number and sometimes the UPC are in "Product information" or "Technical details" (scraper: `model_number`, `product_details`). Variants (color, size, storage) share one page; check which variant the price belongs to.
- **No price on the page** ("See All Buying Options", "price in cart"): run the Amazon scraper before giving up.

## Walmart (walmart.com)

- **URL:** `/ip/<slug>/<itemId>`. The item ID is Walmart-only.
- **Price:** "Now $X", with a struck-through price, "You save" or "Was $Y". "Rollback", "Clearance" and "Reduced price" are Walmart's sale badges.
- **Seller:** "Sold and shipped by Walmart.com" counts. "Sold by <other>" or "Sold and shipped by <other>" is a marketplace seller (scraper: `seller`).
- **Membership:** Walmart+ "early access" deals are members-only during the early window, so they go in the caveats while that lasts.
- **Condition:** "Restored" or "Restored Premium" means refurbished. Open-box offers can sit on the same page, with their own stock line.
- **IDs:** the model number and sometimes the UPC are under "Specifications" (scraper: `gtin`, `upc`, `mpn`). The URL can carry a variant parameter; check that the price belongs to the variant you matched.
- **Language:** Bright Data's fetch sometimes returns the page in Spanish. "Agotado" means sold out; "Disponible" means available.

## Best Buy (bestbuy.com)

- **URL:** `/product/<slug>/<code>/sku/<sku>` (current) or `/site/<slug>/<sku>.p?skuId=<sku>` (older). The SKU is Best Buy-only.
- **Price:** the main price, often with "Was $X" or "Comp. Value $X", and "Save $Y".
- **Model and SKU** are printed under the product title ("Model:", "SKU:"). This is the fastest way to get an exact model number. For Amazon devices the "Model" may be the Amazon ASIN.
- **Membership:** "My Best Buy Plus" or "My Best Buy Total" member prices are paid-membership prices, so they go in the caveats.
- **Condition:** "Open-Box" (Excellent, Good, Fair), "Refurbished" and "Geek Squad Certified Refurbished" are not new. They often show on the same page as cheaper options; ignore them for the verdict.
- **Seller:** Best Buy also hosts marketplace sellers; check the "Sold by" line. Marketplace sellers on the deal's own listing aren't "other sellers" for the verdict.
- Best Buy often sells retailer-specific TV and appliance model numbers. If no other store has that exact model, there is no cross-store verdict.
- If the fetch returns a country-select page instead of the product, fetch it once more; then mark it "couldn't read".

## Target (target.com)

- **URL:** `/p/<slug>/-/A-<TCIN>`. A `preselect=<TCIN>` parameter picks a variant, and the TCIN shown on the page can differ from the one in the URL. Trust the page, not the URL slug: slugs can be years out of date.
- **Price:** the main price, with "reg $X" as the "was" label, and "Sale" or "Deal" badges. Scraper fields: `final_price`, `initial_price`.
- **IDs:** "Specifications" lists TCIN, UPC and DPCI. The UPC here is gold for matching at other stores.
- **Seller:** "Sold & shipped by <other>" means a Target Plus marketplace partner. Note it.
- **Membership:** Target Circle deals need a free Target Circle sign-in, so they count, with a note. The Target Circle Card's 5% off and Target Circle 360 (paid) are caveats only.
- **Condition:** read the highlights too. A "Certified Refurbished" highlight beats a spec line that says "Condition: New"; if the two disagree, flag it and don't treat the unit as new.

## Costco (costco.com) and Sam's Club (samsclub.com)

- Membership warehouses. Treat every price as a member price, so it goes in the caveats and never makes a deal look worse.
- Items are often bundles (extra warranty, accessories, AppleCare) with their own model or item numbers. Compare only when the bundle contents match.
- Some prices are only shown after signing in. Don't sign in; record "price behind member sign-in".

## Newegg, B&H, Micro Center, GameStop

- **Newegg:** many listings are marketplace sellers. "Sold and shipped by Newegg" counts. Combo deals are bundles.
- **B&H:** shows a "B&H #" and the manufacturer number ("MFR #"), often the UPC too. The MFR # is a strong match key. B&H pages read well with a plain fetch.
- **Micro Center:** many prices are in-store only. If the page says so, record "in-store only" and don't count it.
- **GameStop:** sells new, pre-owned and refurbished consoles on similar pages; only "New" counts. Bundles differ by store.

## Maker stores (apple.com, samsung.com, electronics.sony.com, lego.com and others)

- Best source for the "was" check: if the maker sells direct, its own price is the cleanest reference for what anyone charges today. It also counts as one of the two other sellers.
- Apple rarely discounts its own store; Samsung, Sony, Dell and Lenovo run their own sales. Report what the page shows, nothing more.
- Some makers don't sell direct (Hisense, TCL): their pages list the model but no price. Mark them "sells through retailers" and don't count them.
- Some maker prices need a login (education, employee, member pricing). Ignore those.
