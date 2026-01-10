# Docker WordPress Standard

A Docker-based WordPress development environment using **standard WordPress structure** — the same directory layout used by Local by Flywheel, WordPress Studio, and most production hosting environments.

## Features

- Docker-based (no local PHP/MySQL required)
- Standard WordPress structure (`public/` as document root)
- WP-CLI pre-installed
- Mailpit for email testing
- phpMyAdmin for database management
- Optional SSL support
- Multisite support (subdirectory mode)

## Structure

```
docker-wordpress-standard/
├── public/                   ← WordPress root (document root)
│   ├── wp-admin/
│   ├── wp-content/
│   │   ├── plugins/
│   │   ├── themes/
│   │   └── uploads/
│   ├── wp-includes/
│   ├── wp-config.php
│   └── index.php
├── config/                   ← Server configs
│   ├── nginx/
│   └── wordpress/
├── logs/
├── bin/                      ← Helper scripts
├── db/                       ← Database dumps
├── docker-compose.yml
├── docker-compose-ssl.yml
├── .env.example
└── README.md
```

## Quick Start

### 1. Clone and Setup

```bash
git clone https://github.com/hectorjarquin/docker-wordpress-standard.git
cd docker-wordpress-standard
cp .env.example .env
```

### 2. Configure Environment

Edit `.env` to customize:
- `APP_NAME` - Your site name (used for container names and domain)
- `APP_DOMAIN` - Local domain (default: `${APP_NAME}.local`)
- `WP_ADMIN_USER` / `WP_ADMIN_PASSWORD` - WordPress admin credentials
- `WP_PROTOCOL` - `http` or `https`

### 3. Add Local Domain to Hosts File

```bash
# Linux/Mac
sudo echo "127.0.0.1 mysite.local" >> /etc/hosts

# Windows (as Administrator)
echo 127.0.0.1 mysite.local >> C:\Windows\System32\drivers\etc\hosts
```

### 4. Start Containers

```bash
bin/start
```

### 5. Install WordPress

After containers are running, in a new terminal:

```bash
bin/setup-wp
```

### 6. Access Your Site

- **Site**: http://mysite.local (or https:// if SSL enabled)
- **Admin**: http://mysite.local/wp-admin
- **phpMyAdmin**: http://localhost:1337
- **Mailpit**: http://localhost:8025

## Available Commands

| Command | Description |
|---------|-------------|
| `bin/start` | Start all containers |
| `bin/stop` | Stop all containers |
| `bin/down` | Stop and remove containers |
| `bin/restart` | Restart containers |
| `bin/bash` | Enter WordPress container shell |
| `bin/cli [cmd]` | Run command in WordPress container |
| `bin/setup-wp` | Install WordPress |
| `bin/setup-ssl` | Generate SSL certificates |
| `bin/mysql` | Enter MySQL shell |
| `bin/mysql-dump` | Export database |
| `bin/mysql-import [file]` | Import database |
| `bin/openapp` | Open site in browser |
| `bin/phpmyadmin` | Open phpMyAdmin in browser |
| `bin/fix-permissions` | Fix file permissions |

## Using WP-CLI

```bash
# Run WP-CLI commands
bin/cli bash -c "wp plugin list"
bin/cli bash -c "wp theme install flavor flavor activate"
bin/cli bash -c "wp user create john john@example.com --role=editor"
```

## SSL Setup (Optional)

1. Download [mkcert](https://github.com/FiloSottile/mkcert) for your platform
2. Place the executable in `config/nginx/ssl/`
3. Run setup:
   ```bash
   bin/setup-ssl
   ```
4. Change `WP_PROTOCOL=https` in `.env`
5. Restart: `bin/restart`

## Multisite

To enable WordPress Multisite (subdirectory mode):

1. Set `WP_MULTISITE=true` in `.env`
2. Run `bin/setup-wp`

## Development Workflow

### Adding Themes/Plugins

Simply drop themes into `public/wp-content/themes/` and plugins into `public/wp-content/plugins/`. They'll be immediately available in WordPress.

### Version Control

The `.gitignore` excludes WordPress core files (they're downloaded on setup). To track custom themes/plugins, add exceptions:

```gitignore
# Track custom theme
!public/wp-content/themes/my-custom-theme/

# Track custom plugin
!public/wp-content/plugins/my-custom-plugin/
```

## Differences from Composer Version

| Aspect | This Repo (Standard) | Composer Version |
|--------|---------------------|------------------|
| WordPress core | Downloaded on setup | Composer dependency |
| Directory structure | `public/` (flat) | `src/wp/` + `src/wp-content/` |
| Plugin installation | wp-admin or manual | wpackagist + Composer |
| Updates | wp-admin "Update" | `composer update` |
| Compatibility | Local, Studio, most hosts | CI/CD pipelines |

## Requirements

- Docker & Docker Compose
- Linux, macOS, or Windows with WSL2

## License

MIT

## See Also

- [docker-wordpress-composer](https://github.com/hectorjarquin/docker-wordpress-composer) - Bedrock/Composer-based alternative
