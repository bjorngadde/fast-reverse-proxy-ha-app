# Home Assistant App: FRP Server

Runs [frp](https://github.com/fatedier/frp)'s server, `frps`, on your Home
Assistant host. Together with `frpc` on your laptop it works like a
self-hosted ngrok: you get a public URL such as `https://myapp.example.com`
that tunnels to `localhost:3000` on whatever machine runs `frpc`, wherever
that machine is.

```text
internet ──► router :443 ──► reverse proxy (e.g. Nginx Proxy Manager)
                                   │  *.example.com
                                   ▼
                       frps vhost :7080 ◄── tunnel ── frpc on laptop ──► localhost:3000
                                                ▲
internet ──► router :7000 ──► frps bind :7000 ──┘
```

## Installation

1. In Home Assistant, go to **Settings → Apps → App store**, open the menu
   in the top right and choose **Repositories**.
2. Add `https://github.com/bjorngadde/fast-reverse-proxy-ha-app`.
3. Install **FRP Server**. Home Assistant builds the image locally, which
   takes a minute.
4. Set an **Auth token** (at least 16 random characters), then start the app.

## Configuration

| Option | Default | What it does |
| --- | --- | --- |
| `auth_token` | (required) | Shared secret every `frpc` must present. |
| `bind_port` | `7000` | Port `frpc` connects to. Forward this one in your router. |
| `force_tls` | `true` | Reject clients that don't use TLS. `frpc` uses TLS by default. |
| `vhost_http_port` | `7080` | Plain-HTTP port where frps routes tunnels by host name. Your reverse proxy points here. Don't forward it. |
| `subdomain_host` | empty | Base domain for tunnels. With `example.com`, a client asking for subdomain `myapp` is served at `myapp.example.com`. |
| `allow_ports` | empty | Ports clients may open for TCP/UDP tunnels, e.g. `6000-6100, 6200`. Empty means any port. |
| `max_ports_per_client` | `0` | Limit on TCP/UDP ports per client. `0` means no limit. |
| `dashboard_port` | `7500` | Port for the frps web dashboard. |
| `dashboard_user` | `admin` | Dashboard username. |
| `dashboard_password` | empty | The dashboard only runs when a password is set. |
| `metrics_port` | `0` | Prometheus `/metrics` and `/healthz` listener. `0` turns it off. |
| `log_level` | `info` | `trace`, `debug`, `info`, `warn` or `error`. |

The app uses the host network, so every port above is opened directly on the
Home Assistant host. Make sure none of them collide with other apps.

Only `bind_port` (and any TCP/UDP tunnel ports you allow) should be forwarded
in your router. Keep the dashboard, metrics and vhost ports LAN-only.

## Putting HTTPS in front with Nginx Proxy Manager

frps serves the tunnels as plain HTTP on `vhost_http_port`. Let your reverse
proxy terminate TLS and hand the requests to frps:

1. DNS: make sure `*.example.com` (or `*.t.example.com`) resolves to your
   router.
2. In Nginx Proxy Manager, add a **proxy host**:
   - Domain names: `*.example.com`
   - Forward to `http://<home-assistant-ip>:7080`
   - Turn on **Websockets support**.
3. SSL: request a wildcard certificate. Wildcards need the **DNS challenge**,
   so pick your DNS provider and paste its API credentials.

Nginx prefers exact host names over wildcards, so existing hosts such as
`www.example.com` keep working next to the `*.example.com` catch-all.

## Using it from a client

Install `frpc` (a single binary from the
[frp releases](https://github.com/fatedier/frp/releases)), then:

```toml
# frpc.toml
serverAddr = "example.com"
serverPort = 7000
auth.token = "your-auth-token"

[[proxies]]
name = "myapp"
type = "http"
localPort = 3000
subdomain = "myapp"
```

```sh
frpc -c frpc.toml
# → https://myapp.example.com
```

Proxy names must be unique across all connected clients.

## Inspecting and replaying requests

frps has no ngrok-style request inspector. Run one on the client side
instead, for example mitmproxy's web UI:

```sh
mitmweb --mode reverse:http://localhost:3000 -p 8081
# then point the tunnel at localPort = 8081
```

## Dashboard and metrics

With `dashboard_password` set, open `http://<home-assistant-ip>:7500` for live
clients, proxies and per-proxy traffic (7 days of history).

With `metrics_port` set, Prometheus can scrape
`http://<home-assistant-ip>:<metrics_port>/metrics`.
