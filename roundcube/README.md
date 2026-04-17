# Home Assistant Add-on: Roundcube Webmail

![Project Stage][project-stage-shield]
![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

[Roundcube](https://roundcube.net) is a browser-based IMAP webmail client that
talks to any standards-compliant mail server. This add-on runs it inside Home
Assistant and exposes it through the ingress sidebar, so you don't need to
publish any extra ports or reverse-proxy anything yourself.

## Features

- Roundcube 1.7 on PHP 8.4 (Alpine base), with nginx + PHP-FPM.
- Sidebar access through **Home Assistant ingress** — no port forwarding, no
  certificate setup.
- Persistent SQLite database under `/data/`, included in HA backups.
- Drop-in plugin slot at `/share/roundcube/plugins/` for companion add-ons.

## Companion add-ons

- **Roundcube Forward Email** — adds catch-all reply support for
  [Forward Email](https://forwardemail.net) accounts. Install it after this one
  if you want that workflow.

## Getting started

1. Install this add-on.
2. Open **Configuration** and set the IMAP and SMTP hosts for your provider.
   See the **Documentation** tab for prefix/port hints and per-provider
   examples.
3. Start the add-on and open it from the sidebar.

## Support

Issues specific to this add-on: <https://github.com/teh-hippo/addon-roundcube/issues>

For Roundcube itself (rendering, keyboard shortcuts, plugin APIs), see the
[Roundcube project](https://github.com/roundcube/roundcubemail).

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[project-stage-shield]: https://img.shields.io/badge/project%20stage-experimental-yellow.svg
