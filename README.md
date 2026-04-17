# Roundcube Webmail Add-ons for Home Assistant

[![CI](https://github.com/teh-hippo/addon-roundcube/actions/workflows/ci.yaml/badge.svg)](https://github.com/teh-hippo/addon-roundcube/actions/workflows/ci.yaml)

This repository provides two Home Assistant add-ons:

| Add-on                                                 | Purpose                                                                                                          |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| **[Roundcube Webmail](roundcube/)**                    | [Roundcube](https://roundcube.net/) webmail client, exposed via HA ingress. Works with any IMAP/SMTP server.     |
| **[Roundcube Forward Email](roundcube-forwardemail/)** | Optional companion that installs the [Forward Email](https://forwardemail.net) plugin into the Roundcube add-on. |

The Roundcube add-on scans `/share/roundcube/plugins/` on startup and picks
up anything companion add-ons have dropped there — so no rebuild is needed
to add Forward Email (or future companions).

## Installation

1. **Settings → Add-ons → Add-on Store**.
2. Three-dot menu → **Repositories**.
3. Add `https://github.com/teh-hippo/addon-roundcube`.
4. Install **Roundcube Webmail** first; configure IMAP/SMTP; start it.
5. Optionally install **Roundcube Forward Email**; configure API key and
   domain; start it; then restart Roundcube Webmail to load the plugin.

## Documentation

- [Roundcube Webmail — DOCS](roundcube/DOCS.md)
- [Roundcube Forward Email — DOCS](roundcube-forwardemail/DOCS.md)

## License

MIT License — see [LICENSE](LICENSE).
