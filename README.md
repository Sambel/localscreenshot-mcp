# localscreenshot - Screenshot API & MCP Server

Take a screenshot of any public web page from 227 countries, with cookie banners and ads hidden. Use it from your code with the REST API, or from your AI assistant through the Model Context Protocol.

This repository is the public documentation of the hosted service: the MCP endpoint, the tools, the client setups and the registry manifest. The service itself runs at [localscreenshot.com](https://localscreenshot.com), the command line client lives in [localscreenshot-cli](https://github.com/Sambel/localscreenshot-cli).

## Quick start, no API key

Any HTTP MCP client can connect to the free endpoint. Real screenshots, no sign-up:

```json
{
  "mcpServers": {
    "localscreenshot": {
      "type": "http",
      "url": "https://api.localscreenshot.com/mcp/free"
    }
  }
}
```

It answers `initialize`, `tools/list`, `prompts/list` and `resources/list` without credentials, and gives three tools: a real screenshot of one of three example pages, the list of countries, and the prices. It cannot capture a page of your choice.

## With your own key

A new account gets 50 credits, no card: [localscreenshot.com/signup](https://localscreenshot.com/signup). Create the key in the dashboard, under Connect.

```json
{
  "mcpServers": {
    "localscreenshot": {
      "type": "http",
      "url": "https://api.localscreenshot.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

Claude Code, in one line:

```bash
claude mcp add --transport http localscreenshot https://api.localscreenshot.com/mcp --header "Authorization: Bearer YOUR_API_KEY"
```

Setups for each tool, written from that tool's own documentation: [localscreenshot.com/mcp](https://localscreenshot.com/mcp) and [docs.localscreenshot.com/agent](https://docs.localscreenshot.com/agent).

## Tools

With a key:

| Tool | What it does |
|---|---|
| `take_screenshot` | Takes the screenshot. Returns the image, the page title, its final address and status, and the credits left. |
| `list_countries` | Lists the countries a screenshot can be taken from. |
| `get_credits` | Says how many credits are left. |

On the free endpoint:

| Tool | What it does |
|---|---|
| `take_demo_screenshot` | A real screenshot of one of three example pages, in one of two devices. |
| `list_countries` | The same country list, no key needed. |
| `get_pricing` | The credit costs of the service. |

`take_screenshot` accepts the same options as the API, except `async` and `response`: the server waits for the picture and decides how to hand it back. The image is scaled down to 1,568 pixels wide unless you ask for another width, which keeps it readable for a vision model without spending tokens for nothing. A PDF, or an image over 4 MB, comes back as a short-lived download link instead of an attachment. Errors arrive as a plain sentence with their code.

## Options

`take_screenshot` exposes the 28 parameters of the API, minus `async` and `response`: the server waits for the picture and decides how to hand it back.

| Parameter | Type | What it does |
|---|---|---|
| `url` | string | The public http or https page to capture. |
| `country` | string | Load the page from this country, an ISO 3166-1 alpha-2 code. Costs 2 credits instead of 1. The response reports the country actually observed. If it cannot be obtained, the request fails and is not charged. |
| `viewport` | string | Screen size as `WIDTHxHEIGHT`, for example `1440x900`. Shortcut for the two below. |
| `viewport_width` | integer | Width of the browser window, in pixels. |
| `viewport_height` | integer | Height of the browser window, in pixels. With `full_page`, the image is taller than this. |
| `full_page` | boolean | Capture the whole scrollable page instead of the visible part. |
| `device` | string | Capture as this device, meaning screen size, pixel density and mobile browser. One of `desktop`, `desktop_hd`, `laptop`, `ipad`, `ipad_pro`, `ipad_mini`, `iphone_15`, `iphone_15_pro_max`, `iphone_se`, `pixel_8`, `galaxy_s24`. |
| `orientation` | string | Hold a phone or a tablet upright (`portrait`) or on its side (`landscape`). Needs a phone or tablet device. |
| `device_scale_factor` | number | Pixel density. `2` gives a sharper, larger image. |
| `format` | string | `png`, `jpeg`, `webp`, or `pdf` which saves the whole page as a document. |
| `quality` | integer | Quality for `jpeg` and `webp`. Ignored for `png` and `pdf`. |
| `selector` | string | Capture only the first element matching this CSS selector. |
| `max_width` | integer | Scale the image down to this width. Useful to keep it small for a vision model. |
| `click_text` | string | Click one visible button, tab or other clickable element, pointed at by the text a visitor reads, for example "Show all". One click, no typing. Costs 1 more credit. |
| `click_selector` | string | The same click, pointed at by a CSS selector matching exactly one element. Use it when you know the code of the page. One click, no typing. Costs 1 more credit. |
| `wait_for` | string | Wait until an element matching this CSS selector is visible. |
| `delay_ms` | integer | Extra wait before the capture, in milliseconds. |
| `block_cookie_banners` | boolean | Removes most cookie banners. A banner the filters do not know stays in the picture. |
| `block_ads` | boolean | Blocks ads. |
| `block_chat_widgets` | boolean | Hides chat bubbles. |
| `close_popups` | boolean | Closes pop-ups and newsletter overlays. |
| `hide_sticky_elements` | boolean | Hides headers, bars and buttons that stay fixed on screen. |
| `hide_selectors` | array | Hides every element matching these CSS selectors. |
| `disable_animations` | boolean | Freezes animations and transitions. |
| `color_scheme` | string | Ask the page for its `light` or `dark` theme. |
| `language` | string | Browser language, for example `fr` or `fr-FR`. Without it, a screenshot from a country uses the main language of that country. |
| `timezone` | string | Browser time zone, for example `Europe/Paris`. Without it, a screenshot from a country uses the main time zone of that country. |
| `user_agent` | string | User-Agent the page receives. A preset (`googlebot`, `googlebot_mobile`) or your own string. Useful to compare what a site shows to a crawler and to a visitor. A site that checks the crawler's address is not fooled. |

Full details: [Take a screenshot](https://docs.localscreenshot.com/take).

## What it costs

- 1 credit for a screenshot, 2 from a chosen country. A failed screenshot is not charged.
- 50 credits on a new account, once, no card.
- A free account can send 10 requests a minute and run 2 screenshots at a time.
- Plans and credit packs: [localscreenshot.com/pricing](https://localscreenshot.com/pricing).

## Countries

227 countries, from `AD` Andorra onward. The list is served by `GET https://localscreenshot.com/v1/countries`, documented on [Countries and attestation](https://docs.localscreenshot.com/countries). The response of a country-specific capture reports the country actually observed, and a capture that could not be obtained from that country fails and is not charged.

## REST API

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "https://localscreenshot.com/v1/screenshot?url=https://example.com"
```

The response is the image. Add `response=json` for a link to the image plus the page details and the attestation. `POST` with a JSON body works the same way. Background captures return an id, read it at `/v1/screenshots/{id}`.

- OpenAPI: [localscreenshot.com/v1/openapi.json](https://localscreenshot.com/v1/openapi.json)
- Documentation: [docs.localscreenshot.com/overview](https://docs.localscreenshot.com/overview)
- For AI tools: [llms.txt](https://localscreenshot.com/llms.txt)

## Command line

```bash
npx localscreenshot https://example.com --full-page
pipx run localscreenshot https://example.com --country BR
```

Both packages are built from [localscreenshot-cli](https://github.com/Sambel/localscreenshot-cli), with no dependency and no telemetry. The key comes from `LOCALSCREENSHOT_API_KEY` or from `localscreenshot login`.

## What it does not do

It cannot sign in to a website, solve a CAPTCHA or get past a paywall. It captures public pages only.

## Links

- Service: [localscreenshot.com](https://localscreenshot.com)
- Documentation: [docs.localscreenshot.com](https://docs.localscreenshot.com/overview)
- Dashboard and API keys: [localscreenshot.com/dashboard](https://localscreenshot.com/dashboard)
- Command line client: [localscreenshot-cli](https://github.com/Sambel/localscreenshot-cli)
- MCP registry entry: `com.localscreenshot/localscreenshot`

## License

MIT. See [LICENSE](LICENSE).
