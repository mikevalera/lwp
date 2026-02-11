# lwp - Local WP-CLI Wrapper

[![GitHub release](https://img.shields.io/github/v/release/mikevalera/lwp)](https://github.com/mikevalera/lwp/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Run [WP-CLI](https://wp-cli.org/) commands against any [Local](https://localwp.com/) (by Flywheel) site **without entering the site shell**.

No more opening one shell at a time. Target any running WordPress site instantly from any terminal.

```bash
# Instead of opening Local > right-click > Open Site Shell...
lwp --site=mystore plugin list
lwp --site=blog db export backup.sql
lwp --site=client user list
```

## Why?

Local by Flywheel runs each site with its own PHP, MySQL, and unix socket. The only official way to use WP-CLI is through Local's "site shell" — one site at a time, one terminal at a time.

**lwp** fixes that. It reads Local's config, finds the right PHP binary and MySQL socket, and runs WP-CLI with the correct environment. You can target any running site from any terminal window, script WP-CLI across multiple sites, and never touch the site shell again.

## Install

```bash
# One-liner install
curl -sL https://raw.githubusercontent.com/mikevalera/lwp/main/lwp -o /usr/local/bin/lwp && chmod +x /usr/local/bin/lwp
```

Or clone and copy:

```bash
git clone https://github.com/mikevalera/lwp.git
cp lwp/lwp /usr/local/bin/lwp
chmod +x /usr/local/bin/lwp
```

### Requirements

- macOS (Apple Silicon or Intel)
- [Local](https://localwp.com/) installed
- [WP-CLI](https://wp-cli.org/) installed globally (e.g. `/usr/local/bin/wp`)
- Python 3 (ships with macOS)

## Usage

```bash
# Auto-detect site from current directory
cd ~/Local\ Sites/mysite/app/public
lwp plugin list

# Target any site by folder name
lwp --site=mysite option get siteurl
lwp --site=othersite plugin list --status=active

# Database operations (export, import, cli — all work)
lwp --site=mystore db export backup.sql
lwp --site=mystore db import dump.sql

# List running sites
lwp --list

# List all sites (running + stopped)
lwp --list-all
```

## How It Works

1. Reads `~/Library/Application Support/Local/sites.json`
2. Matches the site by folder name (auto-detected from cwd or via `--site=`)
3. Finds the correct PHP binary from Local's `lightning-services/`
4. Finds the MySQL socket at `run/{site_id}/mysql/mysqld.sock`
5. Verifies MySQL is actually responding (not just a stale socket)
6. Sets `MYSQL_UNIX_PORT` so `db export/import/cli` work natively
7. Runs WP-CLI with `php -d mysqli.default_socket=... -d memory_limit=512M`

## Examples

```bash
# Export a database
lwp --site=mystore db export backup.sql

# Search-replace URLs
lwp --site=mystore search-replace 'http://old.local' 'http://new.local'

# Check active plugins across multiple sites
lwp --site=site1 plugin list --status=active
lwp --site=site2 plugin list --status=active

# WooCommerce commands
lwp --site=shop wc product list --format=table
lwp --site=shop wc order list --status=processing

# Cron, cache, users — anything WP-CLI supports
lwp --site=mysite user list
lwp --site=mysite cron event list
lwp --site=mysite cache flush
```

## Compatibility

| | Supported |
|---|---|
| macOS Apple Silicon (M1/M2/M3/M4) | Yes |
| macOS Intel | Yes |
| Local 8.x / 9.x | Yes |
| WP-CLI 2.x | Yes |
| Linux / Windows WSL | Not yet |

## License

MIT
