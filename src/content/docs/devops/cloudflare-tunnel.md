---
title: Cloudflare Tunnel Guide
description: Expose local apps securely with Cloudflare Tunnel, from a quick test URL to a production-ready custom domain.
---

Cloudflare Tunnel lets an app running on your laptop, VPS, or private network receive traffic **without opening inbound ports** or exposing its public IP. The `cloudflared` connector creates outbound, encrypted connections to Cloudflare; Cloudflare then forwards matching requests to your local service.

> **Choose the right option:** use a **Quick Tunnel** for a short-lived demo or webhook test. Use a **named tunnel** with your own domain for staging and production.

## Before you start

You need:

- A local web app, for example `http://localhost:3000`.
- `cloudflared` installed on the computer/server that can reach that app.
- For a custom domain: a Cloudflare account and a domain whose DNS is managed by Cloudflare.

Download the correct `cloudflared` package for Linux, macOS, or Windows from the [official installation page](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/downloads/). Verify it afterwards:

```bash
cloudflared --version
```

For macOS with Homebrew, this is commonly enough:

```bash
brew install cloudflared
```

## 1. Fastest option: Quick Tunnel

Start your app first. For example, a Vite or React development server may run on port `5173`:

```bash
npm run dev
```

In a second terminal, expose that port:

```bash
cloudflared tunnel --url http://localhost:5173
```

The terminal prints a temporary URL like:

```text
https://random-words.trycloudflare.com
```

Open or share that URL. Requests travel through Cloudflare to your local app.

### Great uses for Quick Tunnel

- Testing GitHub, Stripe, Razorpay, or other webhooks on localhost.
- Showing a work-in-progress site to a teammate or client.
- Testing a mobile device against a locally running API.

### Important limitations

- The `trycloudflare.com` URL is random and changes when the process stops.
- It is meant for development and testing, not production.
- It will not start if `~/.cloudflared/config.yml` (or `config.yaml`) already exists. Temporarily move that file or use a named tunnel instead.

## 2. Production option: named tunnel from the dashboard

This is the simplest production setup because Cloudflare stores the tunnel configuration and gives you a token to run on each connector.

1. Sign in to the [Cloudflare dashboard](https://one.dash.cloudflare.com/).
2. Open **Networking → Tunnels** and choose **Create a tunnel**.
3. Name it clearly, for example `portfolio-vps` or `api-production`.
4. Select the server operating system and copy the displayed installation/run command.
5. Run that command on the server. The tunnel should become **Healthy** in the dashboard.
6. Open the tunnel’s **Routes** tab → **Add route** → **Published application**.
7. Enter a hostname such as `app.example.com` and set **Service URL** to your local service, for example `http://localhost:3000`.
8. Save the route and visit `https://app.example.com`.

Cloudflare creates the required proxied DNS route. No inbound `80`, `443`, or application port needs to be opened on your origin server.

> A published application is public by default. If it is an admin panel, preview site, database UI, or internal API, protect it with [Cloudflare Access](https://developers.cloudflare.com/cloudflare-one/applications/configure-apps/self-hosted-apps/).

## 3. CLI-managed named tunnel

Choose this approach when you prefer configuration in files and version control (never commit credentials). First authenticate in a browser:

```bash
cloudflared tunnel login
```

Create a named tunnel:

```bash
cloudflared tunnel create my-app
cloudflared tunnel list
```

The command creates a tunnel UUID and a credentials JSON file. Create `~/.cloudflared/config.yml` and replace the placeholders below:

```yaml
tunnel: YOUR-TUNNEL-UUID
credentials-file: /Users/YOUR_USER/.cloudflared/YOUR-TUNNEL-UUID.json

ingress:
  - hostname: app.example.com
    service: http://localhost:3000
  - hostname: api.example.com
    service: http://localhost:8080
  - service: http_status:404
```

The final catch-all rule is intentional: requests that do not match a hostname receive a `404` instead of reaching an unintended service.

Create the DNS records that point the hostnames to the tunnel:

```bash
cloudflared tunnel route dns my-app app.example.com
cloudflared tunnel route dns my-app api.example.com
```

Validate the configuration, then run it:

```bash
cloudflared tunnel ingress validate
cloudflared tunnel run my-app
```

## 4. Keep it running after a reboot

For a Linux production server, run `cloudflared` as a system service. Provide the complete config path when the configuration belongs to a non-root user:

```bash
sudo cloudflared --config /home/ubuntu/.cloudflared/config.yml service install
sudo systemctl enable --now cloudflared
systemctl status cloudflared
```

Useful service commands:

```bash
sudo systemctl restart cloudflared
sudo journalctl -u cloudflared -f
```

For a dashboard-managed tunnel, use the token command shown by the dashboard. Treat the token like a password: do not put it in screenshots, Git repositories, or frontend code.

## Security checklist

- Keep the origin application bound to `localhost` or a private interface when possible.
- Do **not** open your app’s port in the cloud firewall just because a tunnel exists; `cloudflared` only needs outbound connectivity.
- Restrict access with Cloudflare Access before exposing dashboards, previews, SSH, or internal tools.
- Store tunnel credentials outside the repository and add `.cloudflared/` and `*.json` credential files to `.gitignore`.
- Use separate tunnels/tokens for development, staging, and production.
- Keep `cloudflared` updated and revoke/rotate a token immediately if it is exposed.
- For high availability, run the same tunnel on two or more independent connectors.

## Common problems and fixes

| Problem | Likely cause | What to check |
| --- | --- | --- |
| `502 Bad Gateway` | Cloudflare can reach the tunnel, but the connector cannot reach the local app. | Confirm the service with `curl http://localhost:3000`; verify the protocol and port in the route. |
| Tunnel shows **Inactive** or **Down** | `cloudflared` is not running, its token is invalid, or outbound traffic is blocked. | Check `systemctl status cloudflared` and logs. Ensure outbound port `7844` is allowed. |
| `404` from the tunnel | No ingress rule matched the request. | Check the hostname and ensure the catch-all `http_status:404` rule is last. |
| Website redirects in a loop | Origin HTTPS redirects or Cloudflare SSL settings do not match. | Start with `http://localhost:PORT` as the service URL; only use HTTPS upstream when the origin certificate is valid. |
| Quick Tunnel does not start | A local `config.yml`/`config.yaml` is present. | Move it temporarily or use a named tunnel. |
| Custom hostname does not work | The zone is not active in Cloudflare or DNS is wrong. | Confirm Cloudflare is authoritative for the domain and run `cloudflared tunnel route dns ...` again. |

## Useful commands

```bash
# Test the app locally before blaming the tunnel
curl -I http://localhost:3000

# See named tunnels
cloudflared tunnel list

# Inspect one tunnel and its active connections
cloudflared tunnel info my-app

# Test how ingress rules will route a URL
cloudflared tunnel ingress rule https://app.example.com

# Watch the Linux service logs
sudo journalctl -u cloudflared -f
```

## Helpful tools

- **Cloudflare Zero Trust dashboard:** create and monitor tunnels, routes, and Access rules.
- **`cloudflared` CLI:** install, authenticate, create tunnels, validate ingress, and inspect status.
- **Cloudflare Access:** require email OTP, Google/GitHub login, SSO, or service tokens before a user reaches an app.
- **Cloudflare WARP:** connect users to private network routes through Cloudflare Zero Trust rather than exposing them publicly.
- **Docker:** run the connector in a container when your application is containerized:

  ```bash
  docker run cloudflare/cloudflared:latest tunnel --no-autoupdate run --token YOUR_TUNNEL_TOKEN
  ```

## Official references

- [Cloudflare Tunnel: get started](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/get-started/)
- [Create a dashboard-managed tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/get-started/create-remote-tunnel/)
- [Quick Tunnels / TryCloudflare](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/do-more-with-tunnels/trycloudflare/)
- [Run cloudflared as a Linux service](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/do-more-with-tunnels/local-management/as-a-service/linux/)
