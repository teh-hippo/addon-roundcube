# Roundcube Webmail — Add-on Documentation

For Roundcube itself — mail client features, keyboard shortcuts, skins, plugin
authoring, general errors — refer to the upstream project:

- [Roundcube user guide](https://roundcube.net/help/)
- [Roundcube wiki & FAQ](https://github.com/roundcube/roundcubemail/wiki)
- [Roundcube issue tracker](https://github.com/roundcube/roundcubemail/issues)

## What this add-on provides

- Roundcube 1.7 (PHP 8.4, Alpine base) with nginx + PHP-FPM.
- Exposed through **Home Assistant ingress**; appears in the sidebar with no
  extra ports open.
- Persistent SQLite database under `/data/` (included in HA backups).
- Plugin extension point under `/share/roundcube/plugins/` so companion
  add-ons (e.g. **Roundcube Forward Email**) can inject extra plugins without
  a rebuild.

## Configuration

Settings → Add-ons → Roundcube Webmail → Configuration.

### IMAP / SMTP

| Prefix   | Mode         | Typical port (IMAP / SMTP) |
| -------- | ------------ | -------------------------- |
| `ssl://` | Implicit TLS | 993 / 465                  |
| `tls://` | STARTTLS     | 143 / 587                  |

Examples:

| Provider    | IMAP host                 | SMTP host                 |
| ----------- | ------------------------- | ------------------------- |
| Fastmail    | `ssl://imap.fastmail.com` | `ssl://smtp.fastmail.com` |
| Gmail       | `ssl://imap.gmail.com`    | `ssl://smtp.gmail.com`    |
| Mailbox.org | `ssl://imap.mailbox.org`  | `ssl://smtp.mailbox.org`  |

Changing IMAP/SMTP settings restarts the container in seconds — no rebuild.

### Additional Roundcube plugins

Add composer package names to the `plugins` list, e.g.:

```yaml
plugins:
  - elm/identity_smtp
```

Plugins are installed at container start. A restart is enough — no rebuild.

### Plugin extension point (advanced)

Any subdirectory dropped into `/share/roundcube/plugins/<name>/` is picked up
on Roundcube startup **if** it carries a `.ha-plugin-ready` marker file, is
not a symlink, and its name matches `^[a-zA-Z0-9_-]+$`. Matching trees are
symlinked into Roundcube's plugins folder and registered automatically; a
sibling `config.inc.php` is included after the main Roundcube config. This
is how companion add-ons (e.g. Roundcube Forward Email) extend Roundcube
without rebuilding this image.

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
- If you changed the `plugins` list, a failed composer fetch stops startup.
  Restart after removing the offending package.

### Sidebar shows a blank page or 502

- PHP-FPM may still be warming up. Wait ~10 seconds and refresh.
- If it persists, check **Log** for `php-fpm` restart loops.

### Login fails

- Verify the IMAP host prefix (`ssl://` vs `tls://`) and port match your
  provider.
- If the provider requires app-specific passwords (Gmail, Fastmail), you
  must generate one on their end.

### I changed config — do I need to rebuild?

**No.** Configuration changes trigger a fast container restart (seconds).
A full rebuild is only needed when the add-on version changes (Dockerfile
or rootfs updates).
