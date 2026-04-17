# Home Assistant Add-on: Roundcube Forward Email

![Project Stage][project-stage-shield]
![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

Companion add-on for **Roundcube Webmail** that adds
[Forward Email](https://forwardemail.net) catch-all support. When you reply
from an address on your catch-all domain, it transparently creates the alias
and matching SMTP credentials on the Forward Email side, so the reply goes
out from the right identity without any manual setup.

## Requirements

- **Roundcube Webmail** add-on (from the same repository) — installed and
  running.
- A Forward Email account with a verified domain and an API key.

## How it works

On start, this add-on drops a ready-to-use plugin tree into
`/share/roundcube/plugins/forwardemail/`. The Roundcube Webmail add-on
picks it up on next restart and loads it — no rebuild, no composer fetch
at runtime.

Changing options here triggers a fast restart and re-provisions the plugin;
restart the Roundcube add-on afterward to pick up the new config.

## Getting started

1. Install and start **Roundcube Webmail** first.
2. Install this add-on.
3. Open **Configuration** and set `api_key` and `domain`. See the
   **Documentation** tab for option details.
4. Start it, then restart Roundcube Webmail.

### Logging in with a catch-all domain

This add-on does **not** create a primary mailbox for you — Forward Email is a
forwarder, not a provider. To actually receive mail in Roundcube you need a
destination mailbox that Forward Email routes to, and then either:

- **Recommended: use a catch-all alias** (`*@yourdomain`) on your domain so
  every address on the domain delivers to one mailbox. Set it up per
  <https://forwardemail.net/faq#do-you-support-catch-all-domains> and generate
  an IMAP/SMTP password for the alias from the Forward Email dashboard.
- **Or: create a specific alias** (e.g. `inbox@yourdomain`) and generate a
  password for that.

Use `<alias>@<domain>` and the generated password to log into Roundcube.

### Optional: skip the Roundcube login screen

If you'd rather not type that password every time, set:

- `autologin_user`: the full email you want to log in as (e.g. `inbox@example.com`)
- `autologin_password`: the IMAP/SMTP password generated for that alias
- `autologin`: `true`

With this on, anyone who can reach the Roundcube Ingress URL is logged in as
that identity. Home Assistant's own authentication is the gate — treat this
as equivalent to storing the password in plain text in the add-on config
(which, effectively, it is).

## Support

Issues specific to this add-on: <https://github.com/teh-hippo/addon-roundcube/issues>

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[project-stage-shield]: https://img.shields.io/badge/project%20stage-experimental-yellow.svg
