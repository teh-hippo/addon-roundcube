# Changelog

## Unreleased

### Added

- Icon and logo for the add-on store listing.
- `README.md` so the add-on Info tab is populated.
- Initial release
- Roundcube 1.7-rc6 with PHP 8.4
- SQLite database (no MariaDB dependency)
- HA ingress support (sidebar access)
- Optional direct port access with SSL
- identity_from_to plugin for catch-all alias support
- Watchdog health monitoring

### Fixed

- Reinstall: init script now detects an already-initialized `/data/db/roundcube.db`
  via a `users` table probe rather than treating file presence as authoritative,
  so reinstalls that inherit Supervisor-persisted `/data` no longer abort with
  `FATAL: Database initialization failed`.
