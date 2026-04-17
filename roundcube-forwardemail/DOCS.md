## Roundcube Forward Email — Add-on Documentation

This add-on adds the [Forward Email](https://forwardemail.net) Roundcube
plugin to the **Roundcube Webmail** add-on, so replying to a catch-all
address auto-creates the alias and identity with freshly generated SMTP
credentials.

It is a companion to, not a replacement for, the Roundcube Webmail add-on.

### What it does

On startup this add-on:

1. Clones the Forward Email plugin source from
   [`teh-hippo/roundcube-forwardemail`](https://github.com/teh-hippo/roundcube-forwardemail)
   at a **pinned commit** into a staging folder.
2. Writes a `config.inc.php` alongside it with the options you configured
   here (API key, domain, auto-create, auto-delete).
3. Drops a `.ha-plugin-ready` marker and atomically swaps the staging folder
   into `/share/roundcube/plugins/forwardemail/`.
4. Stays running so that any change to its options triggers a fast restart
   and re-provisions the plugin.

The **Roundcube Webmail** add-on scans `/share/roundcube/plugins/` on its
own startup and only loads entries that carry the readiness marker, so it
never sees a half-written tree. No rebuild needed.

### Configuration

Settings → Add-ons → Roundcube Forward Email → Configuration.

- **API key** — *optional*. Generate under **My Account → Security** on
  [forwardemail.net](https://forwardemail.net/my-account/security). Leave
  blank to use **shared-credential mode** (see below).
- **Domain** — the domain you have registered with Forward Email. Required.
  Must be the bare domain (e.g. `example.com`) — not an email address.
- **Pre-fill From on reply** — default on. Recommended for the catch-all
  workflow.
- **Auto-delete aliases on identity removal** — default off. Only
  meaningful in API-key mode.
- **Autologin** — default off. When on, `autologin_user` and
  `autologin_password` must be set and Roundcube will skip its login screen.
  See the **Autologin** section below for the security tradeoff.
- **Autologin user / password** — the full email and IMAP/SMTP password to
  log in as. Generate the password from the Forward Email dashboard
  (**Domain → Aliases → Generate Password**).

### Modes

The add-on supports two deployment patterns:

#### Shared-credential mode (recommended)

*Leave the API key blank.* Create a single **wildcard alias** `*@yourdomain`
on Forward Email, generate an SMTP password for it, and put the wildcard
alias address + password into the autologin fields.

On reply to a catch-all address, the plugin pre-fills **From** with the
original recipient address and creates a local Roundcube identity. Sending
uses the wildcard alias's authenticated SMTP session — Forward Email
accepts any `From` on the domain because the wildcard alias owns all of
them. Inbound mail goes through the catch-all exactly as before.

No alias churn on Forward Email, no per-reply API calls, no stored
per-identity credentials.

#### Per-alias API mode

*Provide an API key.* On reply, the plugin creates a dedicated alias for
the original recipient via the Forward Email API, generates SMTP
credentials for it, and stores them in Roundcube's prefs. Use this if you
specifically want each reply-as address to exist as its own FE alias with
its own mailbox.

### Logging in — mailbox setup

Forward Email forwards; it does not host mailboxes. To receive mail in
Roundcube you need a destination Forward Email will route to, and then
either:

- **Catch-all** (`*@yourdomain`) — one alias captures every address on the
  domain. Setup guide:
  <https://forwardemail.net/faq#do-you-support-catch-all-domains>
- **Specific alias** — create e.g. `inbox@yourdomain` in the Forward Email
  aliases UI and point it at your real mailbox.

Either way, generate a password for the alias in the Forward Email dashboard
(**Domain → Aliases → click the alias → Generate Password**). Use that
password (and the full alias email) to log into Roundcube.

### Autologin

If you enable `autologin`, the Forward Email Roundcube plugin injects a
synthetic login POST on every anonymous request, so the Roundcube login
form is skipped. It only applies to the single identity you configure.

**Security tradeoff**: anyone who can reach this Roundcube instance is
logged in as that alias. In the HA add-on deployment the gate is HA
Ingress authentication — so you are effectively saying "any HA user who
can see the sidebar is also this email user". That's fine for a
single-user household; avoid if you share HA with family or guests.

The password is stored in the add-on options (HA marks it as `password`,
so the UI hides it) and written into `config.inc.php` under
`/share/roundcube/plugins/forwardemail/` with mode `0640`.

### Order of operations

1. Install the **Roundcube Webmail** add-on first and confirm it works.
2. Install **Roundcube Forward Email** alongside it.
3. Configure this add-on and start it.
4. Check its log for `Plugin provisioned` confirmation.
5. Restart the **Roundcube Webmail** add-on so it picks up the new plugin.

### Troubleshooting

#### "Plugin not picked up"

Check that the Roundcube Webmail add-on has been restarted since this
add-on first started. The shared-folder symlink only resolves at
Roundcube's startup.

#### "API key invalid"

Verify from an HA Terminal shell:

```
curl -u 'YOUR_API_KEY:' https://api.forwardemail.net/v1/domains
```

#### "Provisioning logged an error"

See the add-on log for the exact API response. Common causes: domain not
verified, API key revoked, quota exceeded.

### Data persistence

This add-on writes to `/share/roundcube/plugins/forwardemail/` which lives
in the HA shared folder. Backups include `/share/` by default. The add-on
itself holds no state under `/data/`.
