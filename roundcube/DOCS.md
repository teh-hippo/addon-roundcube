# Roundcube Webmail — Documentation

## About

This add-on provides [Roundcube](https://roundcube.net/), a browser-based IMAP
email client, running inside Home Assistant. It connects to any standard
IMAP/SMTP server and is accessible via the HA sidebar (ingress) or an optional
direct port.

## Configuration

### IMAP and SMTP

Set `imap_host` and `smtp_host` to your email provider's server addresses.
Use `ssl://` prefix for implicit TLS or `tls://` for STARTTLS.

**Examples:**

| Provider      | IMAP Host                     | SMTP Host                     |
| ------------- | ----------------------------- | ----------------------------- |
| Forward Email | `ssl://imap.forwardemail.net` | `ssl://smtp.forwardemail.net` |
| Fastmail      | `ssl://imap.fastmail.com`     | `ssl://smtp.fastmail.com`     |
| Gmail         | `ssl://imap.gmail.com`        | `ssl://smtp.gmail.com`        |
| Mailbox.org   | `ssl://imap.mailbox.org`      | `ssl://smtp.mailbox.org`      |

### Direct Port Access

By default, Roundcube is accessible only through HA ingress (sidebar).
To enable direct access on port 80:

1. In the add-on configuration, map port `80/tcp` to a host port
2. Optionally enable SSL and provide certificate paths

### Catch-All / Alias Email

This add-on includes the
[Forward Email plugin](https://github.com/teh-hippo/roundcube-forwardemail),
which integrates with [Forward Email](https://forwardemail.net) to automatically
manage aliases and per-identity SMTP credentials.

When you add a new sender identity in Roundcube, the plugin:

1. Creates the alias on Forward Email via their API
2. Generates SMTP credentials for that alias
3. Stores the credentials so you can send as that address

**Setup:**

1. Open Roundcube Settings > Forward Email
2. Enter your Forward Email API key
3. Enter your domain (e.g. `itstotally.me`)
4. Create identities as needed — aliases are created automatically

The `identities_level` is set to `0`, allowing any From address. The
`identity_select` plugin auto-selects the correct sender identity when
replying based on the `Delivered-To` header.

### Additional Plugins

You can install additional Roundcube plugins via composer by adding them
to the `plugins` configuration option:

```yaml
plugins:
  - elm/identity_smtp
```

Plugins are installed at container startup. The package name should be
the composer package name (e.g. `vendor/package`).

### Data Persistence

All data is stored in `/data/` inside the add-on container:

- **SQLite database** (`/data/db/roundcube.db`) — user preferences, identities,
  contacts, cached data
- **Encryption key** (`/data/des_key`) — generated once on first start

This data is included in Home Assistant backups automatically.

## Troubleshooting

### Cannot connect to IMAP server

- Verify `imap_host` includes the correct protocol prefix (`ssl://` or `tls://`)
- Check that `imap_port` matches your provider (993 for SSL, 143 for STARTTLS)
- Ensure your credentials are correct (some providers require app-specific
  passwords or generated tokens)

### Ingress shows blank page

- Check the add-on logs for PHP or nginx errors
- Try restarting the add-on
- If using a reverse proxy, ensure WebSocket and header forwarding are configured

### Login fails with "connection refused"

- The IMAP/SMTP server may be unreachable from your HA instance
- Check firewall rules and DNS resolution
