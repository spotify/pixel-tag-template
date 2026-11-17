# Spotify Pixel for Google Tag Manager

A Google Tag Manager web tag template that installs the [Spotify Pixel](https://adshelp.spotify.com/s/article/About-the-Spotify-Pixel-US) and sends conversion events to Spotify Ads Manager, without Custom HTML.

For server-side containers, use the [Spotify Conversions API tag](https://github.com/spotify/conversion-api-tag-template). Send the same **Event ID** from both to count each conversion once.

## Install

Until the template is in the Community Template Gallery:

1. In Google Tag Manager, open **Templates**, then select **New** under **Tag Templates**.
2. Open the overflow menu, select **Import**, and choose `template.tpl` from this repository.
3. Review the permissions and save.

## Set up

Most sites need one of these two setups.

**One tag per event.** Create a **Page View** tag on the **All Pages** trigger, then one tag per conversion (for example **Purchase** on your order confirmation trigger). Every tag loads the pixel if needed, so tag order doesn't matter.

**One tag for everything.** If your site pushes [Google Analytics 4 recommended events](https://developers.google.com/analytics/devguides/collection/ga4/reference/events) to the data layer, create a single tag with **Event** set to **Use the data layer event name** on a Custom Event trigger matching `.*` with regex enabled. These events are mapped for you:

| Data layer event | Spotify event |
| --- | --- |
| `gtm.js`, `page_view` | Page View |
| `view_item` | View Product |
| `add_to_cart` | Add to Cart |
| `begin_checkout` | Start Checkout |
| `purchase` | Purchase |
| `generate_lead` | Lead |
| `sign_up` | Sign Up |

Add your own mappings, for example `newsletter_signup` to Custom Event 1, under **Additional event mappings**. Events without a mapping only load the pixel.

## What the template handles for you

- **One configuration per page.** The pixel is configured by the first tag that fires. A tag with a different pixel ID fails, because the pixel supports one pixel ID per page.
- **Existing installs.** If the pixel snippet is already hard-coded on the page, the template sends events through it instead of loading a second copy.
- **Page views on single-page apps.** The pixel sends a Page View on every URL change, so don't also fire Page View tags on History Change triggers. If you prefer your own triggers, turn off **Track page views on single-page app navigation automatically** under **Advanced**.
- **Ecommerce data.** Value, currency, quantity, order ID, coupon and items are read from the GA4 `ecommerce` object. Push `{ ecommerce: null }` before each ecommerce event so stale values aren't reused.
- **Consent.** By default the template follows Google Consent Mode's `ad_storage`. Until it's granted, the pixel holds events in memory without cookies or network requests, then sends them on grant or drops them on denial. If a visitor denies and later accepts on the same page, the template configures the pixel again and resends the page view; conversions fired while consent was denied are not sent.
- **Install source.** The template tells the pixel it was installed through GTM, along with the template version and the optional **Partner** value under **Advanced**, so these installs can be told apart from hand-installed snippets.
- **User data.** Email and phone number are normalized and SHA-256 hashed by the pixel in the browser. Send raw values: pre-hashed email and phone values are dropped, because hashing them again would never match.

## Requirements

Consent Mode support needs Spotify Pixel SDK 2.6.0 or later, which `pixel.byspotify.com` serves.

## License

Copyright 2026 Spotify AB. Licensed under the [Apache License, Version 2.0](LICENSE).
