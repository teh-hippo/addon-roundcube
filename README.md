# Roundcube Webmail Add-on for Home Assistant

[![CI](https://github.com/teh-hippo/addon-roundcube/actions/workflows/ci.yaml/badge.svg)](https://github.com/teh-hippo/addon-roundcube/actions/workflows/ci.yaml)

A single Home Assistant add-on that runs [Roundcube](https://roundcube.net/)
webmail and exposes it through HA ingress. Works with any IMAP/SMTP server.

Extra functionality comes from Roundcube plugins installed from
[packagist](https://packagist.org/) — drop the package name into the
add-on's `plugins` list and restart.

Recommended companion plugin:

- [`teh-hippo/roundcube-catchall`](https://github.com/teh-hippo/roundcube-catchall)
  — catch-all support (auto-create an identity for the delivered-to address
  when you Reply, so the right `From:` is used).

## Installation

1. **Settings → Add-ons → Add-on Store**.
2. Three-dot menu → **Repositories**.
3. Add `https://github.com/teh-hippo/addon-roundcube`.
4. Install **Roundcube Webmail**; configure `imap_host` and `smtp_host`; start.

## Documentation

- [Roundcube Webmail — DOCS](roundcube/DOCS.md)

## License

MIT License — see [LICENSE](LICENSE).
