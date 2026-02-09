# lwp - Local WP-CLI Wrapper

Run WP-CLI commands against any [Local](https://localwp.com/) (by Flywheel) site **without entering the site shell**.

No more opening one shell at a time. Target any running site instantly from any terminal.

## Install

```bash
cp lwp /usr/local/bin/lwp
chmod +x /usr/local/bin/lwp
```

### Requirements

- macOS (Apple Silicon or Intel)
- [Local](https://localwp.com/) installed
- [WP-CLI](https://wp-cli.org/) installed globally (e.g. `/usr/local/bin/wp`)
- Python 3 (ships with macOS)
- Perl (ships with macOS — used for socket health checks)

## Usage

```bash
# Auto-detect site from current directory
cd ~/Local\ Sites/mysite/app/public
lwp plugin list

# Target any site by folder name
lwp --site=mysite option get siteurl
lwp --site=othersite plugin list --status=active

# List running sites
lwp --list

# List all sites (running + stopped)
lwp --list-all

# Version
lwp --version
```

## How it works

Local runs each site with its own PHP version, MySQL instance, and unix socket. Normally you need to enter a "site shell" to access these — one at a time.

`lwp` reads Local's `sites.json` config, resolves the correct PHP binary and MySQL socket for the target site, and runs WP-CLI with the right environment. No shell switching needed.

Specifically it:
1. Reads `~/Library/Application Support/Local/sites.json`
2. Matches the site by folder name (auto-detected from cwd or via `--site=`)
3. Finds the correct PHP binary from Local's `lightning-services/`
4. Finds the MySQL socket at `run/{site_id}/mysql/mysqld.sock`
5. Verifies MySQL is actually responding (not just a stale socket)
6. Runs WP-CLI with `php -d mysqli.default_socket=... -d memory_limit=512M`

## Examples

```bash
# Export a database
lwp --site=mystore db export backup.sql

# Search-replace URLs
lwp --site=mystore search-replace 'http://old.local' 'http://new.local'

# Check active plugins across sites
lwp --site=site1 plugin list --status=active
lwp --site=site2 plugin list --status=active

# Run any wp-cli command
lwp --site=mysite user list
lwp --site=mysite cron event list
lwp --site=mysite cache flush
```

## License

MIT
