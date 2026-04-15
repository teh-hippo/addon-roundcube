# Roundcube Webmail Add-on for Home Assistant

[![CI](https://github.com/teh-hippo/addon-roundcube/actions/workflows/ci.yaml/badge.svg)](https://github.com/teh-hippo/addon-roundcube/actions/workflows/ci.yaml)
[![Deploy](https://github.com/teh-hippo/addon-roundcube/actions/workflows/deploy.yaml/badge.svg)](https://github.com/teh-hippo/addon-roundcube/actions/workflows/deploy.yaml)

[Roundcube](https://roundcube.net/) is a free and open-source webmail client.
This add-on packages Roundcube for Home Assistant OS, providing a browser-based
email client accessible from the HA sidebar.

## Features

- Roundcube 1.7 with PHP 8.4
- Works with **any IMAP/SMTP server** (Forward Email, Fastmail, Gmail, etc.)
- HA ingress support — access from the sidebar, no extra ports needed
- SQLite database — no MariaDB required
- Automatic identity matching via
  [identity_from_to](https://github.com/sblaisot/identity_from_to) plugin
  (ideal for catch-all/alias email setups)
- Persistent storage included in HA backups

## Installation

1. Navigate to **Settings** → **Add-ons** → **Add-on Store**
2. Click the three-dot menu → **Repositories**
3. Add this URL: `https://github.com/teh-hippo/addon-roundcube`
4. Find **Roundcube Webmail** in the store and click **Install**

## Configuration

Configure your IMAP and SMTP server in the add-on options:

| Option      | Description                                       | Default                  |
| ----------- | ------------------------------------------------- | ------------------------ |
| `imap_host` | IMAP server (e.g., `ssl://imap.forwardemail.net`) | `ssl://imap.example.com` |
| `imap_port` | IMAP port                                         | `993`                    |
| `smtp_host` | SMTP server (e.g., `ssl://smtp.forwardemail.net`) | `ssl://smtp.example.com` |
| `smtp_port` | SMTP port                                         | `465`                    |
| `ssl`       | Enable SSL for direct port access                 | `false`                  |
| `certfile`  | SSL certificate file                              | `fullchain.pem`          |
| `keyfile`   | SSL private key file                              | `privkey.pem`            |
| `log_level` | Logging verbosity                                 | `info`                   |

See [DOCS.md](roundcube/DOCS.md) for detailed documentation.

## License

MIT License — see [LICENSE](LICENSE) for details.
