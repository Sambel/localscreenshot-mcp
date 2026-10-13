# Installing localscreenshot MCP

localscreenshot is a hosted remote MCP server. There is nothing to clone, build or run
locally: the client only needs one URL.

## Free endpoint, no credentials

```json
{
  "mcpServers": {
    "localscreenshot": {
      "type": "streamable-http",
      "url": "https://api.localscreenshot.com/mcp/free"
    }
  }
}
```

Tools: `take_demo_screenshot`, `list_countries`, `get_pricing`. The demo tool returns a real
screenshot of one of three example pages, so the connection can be verified without a key.

## Own key, any page

Create a free account at https://localscreenshot.com/signup (50 credits every month, no card),
then create a key in the dashboard under Connect and add the header:

```json
{
  "mcpServers": {
    "localscreenshot": {
      "type": "streamable-http",
      "url": "https://api.localscreenshot.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

Tools: `take_screenshot`, `list_countries`, `get_credits`.

`take_screenshot` takes a public URL and returns the image, the final address of the page and the
credits left. A PDF, or an image over 4 MB, comes back as a short-lived download link instead of
an attachment, which keeps the response inside a model's context.
