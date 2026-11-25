# Black Friday bullshit detector for Claude

Paste a Black Friday deal into Claude. It finds the same exact product at Amazon, Walmart, Best Buy, Target and the maker's own store, reads their prices live, and tells you one of four things:

- **BULLSHIT**: it's cheaper somewhere else right now. You get the store, the difference and the link.
- **BULLSHIT DISCOUNT**: the discount is fake, the price is fine. Nobody we checked charges the "was" price today.
- **REAL DEAL**: nobody we checked beats it today, and the discount holds up.
- **NO VERDICT**: it couldn't pin down the exact model or compare enough stores, so it says what's missing instead of guessing.

A check takes about 2 minutes.

## First: get your free Bright Data account

The skill reads live store prices through **Bright Data**, so you need a Bright Data account. It's free to start. Use promo code `thehype50` when you sign up through the link below.

### 👉 [brightdata.com/thehype](https://brightdata.com/thehype)

- **$50 in free credits when you sign up.** That's exactly what this skill runs on, so checking your Black Friday deals costs you nothing.
- **Plus a free tier every month:** 5,000 credits, renewing on the 1st.
- **No credit card needed.**
- **No surprise bills.** When your free credits run out, checks just stop. Bright Data only ever charges money you put in yourself.
- One deal check uses about 10 to 16 credits.

## Set it up (about 5 minutes)

### 1. Get your Bright Data API token

1. Sign up at [brightdata.com/thehype](https://brightdata.com/thehype) with promo code `thehype50`.
2. Open [brightdata.com/cp/setting/users](https://brightdata.com/cp/setting/users) and copy your API token. New accounts also get it in the welcome email.

Treat the token like a password: anyone who has it can spend your credits.

### 2. Connect Bright Data to Claude

In Claude (the website or the desktop app):

1. Go to **Customize → Connectors**.
2. Click **+ Add**, then **Add custom connector**.
3. Name: `Bright Data`.
4. URL: `https://mcp.brightdata.com/mcp?token=YOUR_TOKEN`, with your token in place of `YOUR_TOKEN`.
5. Click **Continue**. If Claude asks how to sign in, choose **No sign in**: the token in the URL already does that job.
6. Click **Add**.

On a Team or Enterprise plan, an Owner adds the connector under **Organization settings → Connectors**, and then you click **Connect** next to it in **Customize → Connectors**.

### 3. Add the skill

1. Go to **Settings → Capabilities** and turn on **Code execution and file creation**. Skills don't run without it.
2. Go to **Customize → Skills**, click **+**, then **+ Create skill**, then **Upload a skill**.
3. Pick `black-friday-bullshit-detector.zip`. Upload the zip as it is; don't unzip it.

### 4. Try it

1. Start a new chat.
2. Click **+ → Connectors** and make sure Bright Data is on.
3. Paste a deal: `Is this a good deal? <link>`

You can also name a product and price instead of a link: `Is $149 for AirPods Pro 3 at Amazon a good deal?`

## Which Claude plans work

- **Custom connectors:** every plan. Free accounts get one custom connector.
- **Uploading a skill:** Pro, Max, Ultra, Team and Enterprise.

## What you get back

A one-line verdict with the reason, then a short table (store, price, in stock, link, notes), then anything that changes the answer: a member-only price, a marketplace seller, open-box or refurbished units, a different bundle or variant, a price that only shows in the cart, shipping.

## What it can't do

- **Price history.** It reads today's pages, so it never claims what a price used to be.
- **Target in a hurry.** Target's lookup often takes 2 to 4 minutes. For a deal from another store, a quick check lists Target under "Couldn't check" and offers to wait for it.
- **Member-only and in-store prices.** Costco member prices, login-only deals and in-store prices show up as notes at most.
- **Store-exclusive models.** If only one store sells that exact model number, there's nothing to compare.

## Using Claude Code instead

```bash
claude mcp add --transport http brightdata "https://mcp.brightdata.com/mcp?token=YOUR_TOKEN"
```

Then put this folder in your project's `.claude/skills/`, so you end up with `.claude/skills/black-friday-bullshit-detector/SKILL.md`.

---

Made by [thehype](https://thehype.news). Live store data by [Bright Data](https://brightdata.com/thehype).
