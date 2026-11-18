# Changelog

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
