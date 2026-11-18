# fast-reverse-proxy-ha-app

[frp](https://github.com/fatedier/frp) packaged as a Home Assistant app.

There are existing apps for the frp *client*. This repository provides the
*server*, `frps`, so your Home Assistant host can act as your own ngrok:
`frpc` on any machine gets a public URL on your domain.

## Apps

- [FRP Server](frps/) (`frps`)

## Add the repository

[![Add repository to Home Assistant][repo-badge]][repo-link]

Or go to **Settings → Apps → App store → ⋮ → Repositories** and add:

```text
https://github.com/bjorngadde/fast-reverse-proxy-ha-app
```

[repo-badge]: https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg
[repo-link]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fbjorngadde%2Ffast-reverse-proxy-ha-app
