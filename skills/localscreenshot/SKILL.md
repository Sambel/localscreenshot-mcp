---
name: localscreenshot
description: Capture any public web page as a screenshot or a PDF, from one of 227 countries, with cookie banners and ads hidden.
---

# localscreenshot

## When to use it

Use it when the user needs to see what a page looks like, or to keep a picture of a page:
a visual check of a deployment, a screenshot for a report or a bug ticket, an archive of a page
that may change, or the rendering of a page as a visitor in another country sees it.

Do not use it for text extraction. If the goal is to read the content of a page, a fetch and a
parse is cheaper and exact. This tool returns an image or a PDF.

## Tools

| Tool | What it does |
|---|---|
| `take_screenshot` | Captures a page. Returns the image, the page title, its final address and status, and the credits left. |
| `list_countries` | Lists the countries a capture can be taken from. |
| `get_credits` | Reports the credits left. |

## Taking a capture

The only required argument is `url`, a public http or https address.

- `viewport`: screen size as `WIDTHxHEIGHT`, for example `1440x900`, `390x844` for a phone.
- `full_page`: the whole page instead of the visible part.
- `format`: `png`, `jpeg`, `webp` or `pdf`.
- `country`: an ISO 3166-1 alpha-2 code, to load the page from that country. The response reports
  the country actually observed. A capture that cannot be taken from the requested country fails
  and is not charged.
- `dark_mode`: asks the page for its dark theme.
- `hide_cookie_banners` and the ad-blocking options are on by default, so the picture is the page
  and not the consent dialogue on top of it.

The image is scaled down to 1,568 pixels wide unless another width is asked for, which keeps it
readable for a vision model without spending tokens for nothing. A PDF, or an image over 4 MB,
comes back as a short-lived download link instead of an attachment.

## Reading the result

The response carries the final address and the HTTP status of the page, so a capture of a
redirected or erroring page is distinguishable from a successful one. Check the status before
describing what the image shows: a screenshot of a 404 is a valid capture of a broken page, not a
failure of the tool.

## Errors

Errors come back as a plain sentence with their code. A capture that fails is not charged.
