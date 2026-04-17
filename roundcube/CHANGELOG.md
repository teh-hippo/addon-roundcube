# Changelog

## Unreleased

### Changed

- **BREAKING**: Removed `imap_port` and `smtp_port` config fields. Specify the
  port inline in `imap_host` / `smtp_host` (e.g. `ssl://imap.example.com:993`).
- **BREAKING**: Removed the nested `catchall:` config block. For catch-all
  support, add `teh-hippo/roundcube-catchall` to the `plugins` list and
  configure the plugin via its own `config.inc.php`.
- Removed the unused `/share/roundcube/plugins/` shared-plugin scanner. All
  plugins now flow through the composer/packagist path.
- Rewrote `DOCS.md` / `README.md` without Forward Email-specific references.
- Repository name in `repository.yaml` is singular ("Roundcube Webmail add-on").

### Fixed

- First-run database init now clears SQLite WAL/journal/shm sidecar files in
  addition to the main `roundcube.db`, so stale WAL replay no longer surfaces
  as `table users already exists` during `initdb.sh`.
- Reinstall: init script already detects an already-initialized
  `/data/db/roundcube.db` via a `users` table probe rather than treating file
  presence as authoritative, so reinstalls that inherit Supervisor-persisted
  `/data` no longer abort with `FATAL: Database initialization failed`.

### Added

- Icon and logo for the add-on store listing.
- `README.md` so the add-on Info tab is populated.
- Initial release
- Roundcube 1.7-rc6 with PHP 8.4
- SQLite database (no MariaDB dependency)
- HA ingress support (sidebar access)
- Watchdog health monitoring
