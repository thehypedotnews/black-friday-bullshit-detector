# Matching the exact product

SKILL.md Step 4 has the core rules (confirmed, probable, lookalike, store exclusive) and the traps seen in tests. This file adds the detail and the traps by product type. When in doubt, it's not a match.

## Identifiers, strongest first

1. **UPC / GTIN / EAN.** Same code means the same product, variant and pack. Target lists the UPC under Specifications; Walmart's scraper returns `gtin`; Amazon and Walmart pages sometimes show it. A UPC in quotes is the best search query.
2. **Manufacturer model number / part number (MPN)**, the full string including every suffix: `UN55M70HBFXZA` is not `UN55M70HAFXZA`. Apple part numbers (like `MDHE4LL/A`) encode configuration and color. A listing that shows only the series ("M70H", "The Frame 55") hasn't told you the model yet.
3. **Brand + full name + every variant attribute.** Only when no code is shown on one of the pages. Such a match is "probable": it appears in the table marked "probable" and counts as one of the two other sellers for REAL DEAL and BULLSHIT DISCOUNT, but never as a cheaper offer. It needs nothing conflicting on either page (no different model number, suffix or code). If a probable match is meaningfully cheaper, confirm it (the Amazon scraper returns `model_number`, the Walmart scraper `gtin` and `mpn`); if you can't, the verdict is NO VERDICT.

Store item IDs (Amazon ASIN, Walmart item ID, Best Buy SKU, Target TCIN or DPCI, Costco item number) belong to one store. Never compare them across stores. The one exception: Amazon's own devices (Echo, Kindle, Fire TV, Ring, Blink) often carry the Amazon ASIN as the model number at Best Buy and other stores, and then it works as a model number. URL slugs are not identifiers: they are often stale or generic.

## Everything that must match

- brand and model number, including suffix letters and model year;
- size (55" vs 65"), capacity (5-qt vs 6-qt), storage and memory (16GB/512GB vs 16GB/1TB), and chip or core counts where the line has several;
- generation ("AirPods Pro 2" vs "AirPods Pro 3", "Echo Dot" of different years, "WH-1000XM5" vs "WH-1000XM6");
- color or finish, when the store prices colors differently;
- connectivity: Wi-Fi vs cellular, carrier-locked vs unlocked, disc vs digital console;
- pack count and bundle contents (console with or without a game or subscription code, extra warranty, gift card, accessories);
- condition: new only. Refurbished, renewed, restored, pre-owned and open-box never match a new item.

## Traps by product type

- **TVs.** Retailers sell their own model numbers: the same panel with a different suffix or series code, sold at one store only (Walmart's M70HB vs everyone else's M70HA). Model years matter (Samsung's 2025 vs 2026 The Frame look identical in a listing). Different-size TVs share a series name and often one page. If the exact model number only appears at one store, say "only <store> sells this exact model" and give no cross-store verdict. You may name the closest relative in the notes as "similar, not the same".
- **Laptops and tablets.** Match CPU or chip, GPU core count, RAM, storage, screen size and color. The same "16GB/512GB" can differ in GPU cores across Apple configurations. Prices often differ by color.
- **Phones.** Storage, carrier vs unlocked, and region. Carrier deals that need activation are not a price anyone pays up front; put them in the caveats.
- **Headphones and earbuds.** Generation and color; charging-case type on older earbuds. Last year's model on sale next to this year's is a classic lookalike. Limited-edition colors can carry their own UPC and price.
- **Consoles and games.** Bundle contents differ by store (included game as a disc or a download code, included online-membership code, extra controller). Two listings at the same store can carry different UPCs for different bundles. Marketplace sellers often list bundles well above retail; those are higher offers, not matches, unless the contents match.
- **Smart speakers and displays.** Several generations share one name; kids editions, "with clock" versions and bulb bundles are separate products.
- **Kitchen appliances and vacuums.** Retailer-specific model numbers are common (stick vacuums especially: Best Buy's IA1121 vs SharkNinja's IA1241). Capacity and color change the price.
- **LEGO.** The set number is the identifier. Match on it.
- **Store and marketplace brands.** An item with no model number, sold only by one marketplace seller, usually has no exact match anywhere. Say so.

## When the deal page and the other page disagree

- If a page's highlights say "refurbished" but the spec says "new", treat it as not new and flag it.
- If the price belongs to a different variant than the one in the title (common on variant-heavy pages), find the price for the matched variant or record "couldn't read".
- If you matched by name only (no code on either page), call it "probable" and show it. It can support "lowest" and "nobody charges the was price", never "cheaper elsewhere".
