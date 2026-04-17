# Roundcube Webmail — Add-on Documentation

For Roundcube itself — mail client features, keyboard shortcuts, skins, plugin
authoring, general errors — refer to the upstream project:

- [Roundcube user guide](https://roundcube.net/help/)
- [Roundcube wiki & FAQ](https://github.com/roundcube/roundcubemail/wiki)
- [Roundcube issue tracker](https://github.com/roundcube/roundcubemail/issues)

## What this add-on provides

- Roundcube 1.7 (PHP 8.4, Alpine base) with nginx + PHP-FPM.
- Exposed through **Home Assistant ingress** — appears in the sidebar with no
  extra ports open.
- Persistent SQLite database under `/data/` (included in HA backups).
- Optional composer-installed plugins from packagist.

## Configuration

Settings → Add-ons → Roundcube Webmail → Configuration.

### IMAP / SMTP

Provide `imap_host` and `smtp_host` as **full URLs** — prefix and port
included. The prefix chooses the encryption mode.

| Prefix   | Mode         | Typical IMAP port | Typical SMTP port |
| -------- | ------------ | ----------------- | ----------------- |
| `ssl://` | Implicit TLS | 993               | 465               |
| `tls://` | STARTTLS     | 143               | 587               |

Examples:

| Provider      | `imap_host`                   | `smtp_host`                   |
| ------------- | ----------------------------- | ----------------------------- |
| Fastmail      | `ssl://imap.fastmail.com:993` | `ssl://smtp.fastmail.com:465` |
| Gmail         | `ssl://imap.gmail.com:993`    | `ssl://smtp.gmail.com:465`    |
| Mailbox.org   | `ssl://imap.mailbox.org:993`  | `ssl://smtp.mailbox.org:465`  |
| Forward Email | `ssl://imap.forwardemail.net:993` | `ssl://smtp.forwardemail.net:465` |

Changing IMAP/SMTP settings restarts the container in seconds — no rebuild.

### Additional Roundcube plugins

Add packagist package names to the `plugins` list. They are fetched with
composer on container start.

```yaml
plugins:
  - teh-hippo/roundcube-catchall
  - elm/identity_smtp
```

Each package installs into `plugins/<name>` (hyphens become underscores) and
is added to Roundcube's active plugin list.

#### Catch-all mailboxes

If every address on your domain lands in one inbox (wildcard alias, catch-all),
add [`teh-hippo/roundcube-catchall`](https://github.com/teh-hippo/roundcube-catchall)
to the `plugins` list. The plugin auto-creates an identity for the
delivered-to address on Reply, so the next reply uses the right `From:`.

Plugin-specific options (identity auto-create) live in the plugin's
own `config.inc.php` — see its README.

#### Persistent login / Remember Me

To add a "Remember Me" checkbox to the login form (or enable fully automatic
login), add [`teh-hippo/roundcube-remember-me`](https://github.com/teh-hippo/roundcube-remember-me)
to the `plugins` list. See the plugin README for configuration options.

## Data persistence

- `/data/db/roundcube.db` — SQLite (identities, contacts, preferences).
- `/data/des_key` — encryption key, generated once on first start.

HA backups include `/data/` automatically.

## Troubleshooting (add-on specific)

For **anything that feels like Roundcube itself** — rendering glitches,
compose quirks, keyboard shortcuts, skin issues — see the Roundcube links
at the top of this document. The items below are specific to the HA add-on.

### The add-on won't start

- Check the **Log** tab for PHP-FPM or nginx errors.
- If you added something to the `plugins` list, a failed composer fetch stops
  startup. Remove the offending package and restart.

### Sidebar shows a blank page or 502

- PHP-FPM may still be warming up. Wait ~10 seconds and refresh.
- If it persists, check **Log** for `php-fpm` restart loops.

### Login fails

- Verify the `imap_host` prefix (`ssl://` vs `tls://`) and port match your
  provider.
- If the provider requires app-specific passwords (Gmail, Fastmail), you must
  generate one on their end.

### I changed config — do I need to rebuild?

**No.** Configuration changes trigger a fast container restart (seconds).
A full rebuild is only needed when the add-on version changes (Dockerfile
or rootfs updates).
