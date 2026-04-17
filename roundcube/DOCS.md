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

### Catch-all mailbox

If you use a wildcard alias (e.g. via Forward Email) where every address on
your domain lands in one mailbox, enable the built-in catch-all support.

```yaml
catchall:
  enabled: true
  autologin: true
  autologin_user: inbox@example.com
  autologin_password: "your-app-password"
  identity_autocreate: true
```

What each flag does:

- `enabled` — installs the
  [`teh-hippo/roundcube-catchall`](https://github.com/teh-hippo/roundcube-catchall)
  plugin at startup. Required for any of the other flags to take effect.
- `autologin` + `autologin_user` + `autologin_password` — bypass the
  Roundcube login screen when the add-on is opened through HA ingress. The
  credentials above are stored in plain text inside the add-on options
  (Supervisor holds `options.json` with root-only permissions).
- `identity_autocreate` — when you reply to a message, the plugin adds the
  `To:` address as a Roundcube identity so the next reply uses it as the
  `From:`. Leave enabled for a true catch-all experience.

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
