# Context: HubSpot — Attribution Reporting

## Company

**Name:** HubSpot

**Description:** HubSpot is a CRM platform that unifies marketing, sales, and service data into a single system of record for revenue teams.

## Feature Domain

**Domain:** Attribution Reporting

**Scope:** How HubSpot assigns revenue credit to touchpoints, channels, campaigns, and reps across the full customer journey — from first anonymous visit to closed-won deal. Owned by the Reporting group.

**Target users:**
- **Marketing ops / demand gen managers** — need to justify channel spend and prove marketing's contribution to revenue
- **RevOps leaders** — want a single source of truth across marketing, sales, and service touchpoints
- **CMOs / VPs of Marketing** — need board-ready attribution dashboards that tie campaigns to ARR
- **Sales managers** — want to understand which marketing-sourced touches assisted their deals

**Existing capabilities:**
- First-touch, last-touch, linear, time-decay, and U-shaped (position-based) attribution models
- Multi-touch revenue attribution reports (contacts and deals)
- Campaign influence reporting (which campaigns touched contacts in a deal)
- Original source / source drill-down on contacts and companies
- Custom attribution reports in the report builder
- Deal and contact activity timelines showing all touchpoints
- UTM parameter tracking and ad attribution via Google, Meta, LinkedIn integrations
- Revenue attribution by asset (landing pages, emails, blog posts, CTAs)

**Known pain points:**
- **Model opacity** — users can't explain to stakeholders *why* a specific model assigned credit the way it did; no plain-language breakdown per deal
- **Long sales cycles break attribution windows** — default lookback windows (90 days) miss touches on 6–12 month enterprise deals; customizing windows is buried and confusing
- **Offline and dark touchpoints go untracked** — field events, direct mail, word-of-mouth, and "dark social" shares create attribution gaps that inflate direct traffic credit
- **Cross-object complexity** — attributing revenue to a specific campaign when the contact, company, and deal are all separate objects with separate timelines is non-obvious
- **No model comparison view** — users can't run two models side-by-side on the same deal set to understand the delta before committing to a model
- **AI/assisted touches undervalued** — chat interactions, sequence steps, and rep-assisted demos don't surface clearly in attribution models despite being high-signal
- **Data latency** — attribution reports can lag 24–48 hours, making them unsuitable for live pipeline reviews
- **Custom model creation requires workarounds** — truly custom weighting (e.g., "weight demo bookings 3x") requires calculated properties hacks rather than a first-class model builder

**Competing approaches:**
- **Bizible / Marketo Measure (Adobe)** — deeper enterprise multi-touch attribution, expensive and Adobe-ecosystem only
- **Rockerbox** — independent attribution layer across all channels, strong for media mix; doesn't live inside the CRM
- **Triple Whale / Northbeam** — strong for e-commerce/DTC, not B2B SaaS
- **Salesforce Marketing Cloud Attribution** — native to SFDC orgs; HubSpot differentiates on ease-of-use
- **Google Analytics 4 (Data-Driven Attribution)** — free, strong on web channels, siloed from CRM revenue data

**Goals:**
- Become the default attribution layer for mid-market B2B companies that don't want a standalone tool
- Improve confidence in marketing's revenue contribution so CMOs don't second-guess the data
- Close the gap with Bizible on enterprise deal complexity without sacrificing ease-of-use
- Enable AI-assisted attribution insights (anomaly detection, recommended model per deal type)
