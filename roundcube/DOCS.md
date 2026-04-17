# Roundcube Webmail — Add-on Documentation

This page covers how the **Home Assistant add-on** wraps Roundcube. For
Roundcube itself — mail client features, keyboard shortcuts, skins, plugin
authoring, general errors — refer to the upstream project:

- [Roundcube user guide](https://roundcube.net/help/)
- [Roundcube wiki & FAQ](https://github.com/roundcube/roundcubemail/wiki)
- [Roundcube issue tracker](https://github.com/roundcube/roundcubemail/issues)

## What this add-on provides

- Roundcube 1.7 (PHP 8.4, Alpine base) with nginx + PHP-FPM.
- Exposed through **Home Assistant ingress**, so it appears in the sidebar
  with no extra ports open.
- Persistent SQLite database stored under `/data/` (included in HA backups).
- Optional bundled [Forward Email plugin](https://github.com/teh-hippo/roundcube-forwardemail)
  that auto-creates aliases and SMTP credentials when you add identities.

## Configuration

Configure via **Settings → Add-ons → Roundcube Webmail → Configuration**.

### IMAP / SMTP server

Point the add-on at your provider. Include a protocol prefix:

| Prefix   | Meaning        | Typical port (IMAP / SMTP) |
| -------- | -------------- | -------------------------- |
| `ssl://` | Implicit TLS   | 993 / 465                  |
| `tls://` | STARTTLS       | 143 / 587                  |

Examples:

| Provider      | IMAP host                     | SMTP host                     |
| ------------- | ----------------------------- | ----------------------------- |
| Forward Email | `ssl://imap.forwardemail.net` | `ssl://smtp.forwardemail.net` |
| Fastmail      | `ssl://imap.fastmail.com`     | `ssl://smtp.fastmail.com`     |
| Gmail         | `ssl://imap.gmail.com`        | `ssl://smtp.gmail.com`        |
| Mailbox.org   | `ssl://imap.mailbox.org`      | `ssl://smtp.mailbox.org`      |

> Changing IMAP/SMTP settings restarts the container in seconds — no rebuild.

### Forward Email integration (optional)

If you use [Forward Email](https://forwardemail.net), filling these fields
enables the bundled plugin:

- `forwardemail_api_key` — from **My Account → Security** on Forward Email.
- `forwardemail_domain` — e.g. `example.com`.
- `forwardemail_auto_create` — create aliases + identities on reply to
  catch-all addresses.
- `forwardemail_auto_delete` — remove aliases when the matching identity is
  deleted. Leave off unless you really want that.

Leave `forwardemail_api_key` blank to disable the plugin entirely.

### Additional Roundcube plugins

Add composer package names to the `plugins` list, e.g.:

```yaml
plugins:
  - elm/identity_smtp
```

Plugins are installed at container start. A restart is enough — no rebuild.

## Data persistence

All state lives under `/data/` inside the container:

- `/data/db/roundcube.db` — SQLite (identities, contacts, preferences).
- `/data/des_key` — encryption key, generated once on first start.

HA backups include `/data/` automatically.

## Troubleshooting (add-on specific)

For **anything that feels like Roundcube itself** — rendering glitches,
compose quirks, keyboard shortcuts, skin issues — see the Roundcube links
at the top of this document. The items below are specific to the HA add-on.

### The add-on won't start

- Check **Log** tab for PHP-FPM or nginx errors.
- If you just changed the `plugins` list, a failed composer fetch will stop
  startup. Restart after removing the offending package.

### Sidebar shows a blank page or 502

- PHP-FPM may still be warming up. Wait ~10 seconds and refresh.
- If it persists, check **Log** for `php-fpm` restart loops.

### Login fails

- Verify the IMAP host prefix (`ssl://` vs `tls://`) and port match your
  provider.
- For Forward Email, the password is the **SMTP password for the alias**,
  not the Forward Email account password.
- If the provider requires app-specific passwords (Gmail, Fastmail), you
  must generate one on their end.

### Forward Email plugin not provisioning

- Confirm `forwardemail_api_key` and `forwardemail_domain` are set.
- Check add-on log for `[forwardemail]` entries and API errors.
- API key validity: run from HA Terminal:
  ```
  curl -u 'YOUR_API_KEY:' https://api.forwardemail.net/v1/domains
  ```

### I changed config — do I need to rebuild?

**No.** Changing any Configuration option triggers a fast container restart
(seconds). A full rebuild is only needed when the add-on version changes
(Dockerfile / rootfs updates).
