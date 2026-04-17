# Home Assistant Add-on: Roundcube Catch-all

![Project Stage][project-stage-shield]
![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

Companion add-on for **Roundcube Webmail**. Makes a catch-all mailbox
(`*@yourdomain`) feel like a real multi-identity inbox:

- Replying to any address on your domain pre-fills **From** with that address
  and creates a matching Roundcube identity on the fly.
- Optional autologin — skip the Roundcube login screen entirely.

Works with any catch-all setup: Forward Email, Fastmail, self-hosted Postfix,
anywhere IMAP/SMTP accepts arbitrary `From` on the domain.

## Requirements

- **Roundcube Webmail** add-on (from the same repository) — installed and
  running.
- A mail domain with a catch-all destination and IMAP/SMTP access.

## How it works

On start, this add-on drops a ready-to-use plugin tree into
`/share/roundcube/plugins/roundcube_catchall/`. The Roundcube Webmail add-on
picks it up on next restart — no rebuild needed.

Changing options here triggers a fast restart and re-provisions the plugin.
Restart the Roundcube add-on afterward to load the new config.

## Getting started

1. Install and start **Roundcube Webmail** first.
2. Install this add-on.
3. Configure `autologin_user` + `autologin_password` (or leave autologin off
   and sign in manually). The catch-all domain is derived from the logged-in
   user's address. See the **Documentation** tab for all options.
4. Start it, then restart Roundcube Webmail.

## Support

Issues: <https://github.com/teh-hippo/addon-roundcube/issues>

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[project-stage-shield]: https://img.shields.io/badge/project%20stage-experimental-yellow.svg
