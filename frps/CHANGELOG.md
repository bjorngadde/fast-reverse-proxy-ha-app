# Changelog

## 0.72.0.4

- New `certfile`/`keyfile` options: frps presents a real certificate from
  `/ssl` to frpc, so clients can verify the server with
  `transport.tls.useSystemCa = true`. Without them frps keeps its
  self-signed certificate and logs a warning.
- frps restarts by itself when that certificate is renewed.

## 0.72.0.3

- `auth_token` is optional when users are configured. Without it there is no
  admin login and everyone signs in as a user. Without users it's still
  required.

## 0.72.0.2

- New `users` option: per-person tokens and allowed subdomains, enforced by
  a small frps server plugin (`frps-users`) built into the image. Users can
  only create HTTP tunnels on their own subdomains.
- With users configured, all clients sign in with `metadatas.token`
  instead of `auth.token` (see the Users section in the docs). Without users
  nothing changes.

## 0.72.0.1

- The frps dashboard is now a Home Assistant sidebar panel (**FRP**), served
  through ingress with no extra login.
- Without `dashboard_password` the dashboard listens on localhost only;
  with it, it's also on the LAN as before.
- Docs: setup with the NGINX Home Assistant SSL proxy app, and a note on
  when UDP forwarding is needed.

## 0.72.0

- First release, packaging frp v0.72.0.
- Token auth (token kept in its own file), TLS required by default.
- HTTP vhost routing by subdomain, optional allowed-port range.
- Optional dashboard (only when a password is set) and Prometheus metrics
  listener.
