# PrestaShop LAMP Installation Guide

## Installation Instructions

To set up your PrestaShop LAMP environment, simply run:

### Requirements
- mkcert
- docker
  
#### Mkcert - Windows Users

1. Install Chocolatey by running the following command in PowerShell:
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force;
   [System.Net.ServicePointManager]::SecurityProtocol =
   [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex
   ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```
2. Install `mkcert` using Chocolatey:
   ```powershell
   choco install mkcert
   ```
3. Run:
   ```powershell
   mkcert -install
   ```
4. Generate SSL certificates:
   ```powershell
   mkcert prestashop.loc 127.0.0.1 localhost ::1
   ```
5. Move the generated certificates to the SSL folder inside your LAMP directory in WSL:
   ```sh
   sudo cp ./prestashop.loc+3.pem /<lamp directory>/config/ssl/prestashop-lamp.pem
   sudo cp ./prestashop.loc+3-key.pem /<lamp directory>/config/ssl/prestashop-lamp-key.pem
   ```

#### Mkcert - Linux / MacOS Users
```sh
brew install mkcert
mkcert -install
```

## Lamp Instalation
```sh
git clone https://github.com/pvujic/prestashop-lamp
cd prestashop-lamp
sh install.sh
```

The script will prompt you for configuration options including:

- Docker project name
- PHP version (php82 = PHP 8.2.34)
- Database type (mysql8)
- PrestaShop version (8.2.3)

Once completed, it will download and install PrestaShop along with the required dependencies.

## Running the Environment

Once the installation is complete, start the environment with:

```sh
docker compose up -d
```

You can then access PrestaShop via:

```sh
https://localhost
```

This will open the PrestaShop installation assistant.

## Switching an existing install to PHP 8.2

The `php82` image is pinned to PHP 8.2.34, the same version as pre4. To move an existing environment from another PHP version, run this from the LAMP directory:

```sh
sed -i 's/^PHPVERSION=.*/PHPVERSION=php82/' .env
docker compose up -d --build --no-deps webserver
docker compose exec webserver php -v
```

Only the `webserver` container is rebuilt; the database, Redis and your `www` folder are not touched. To go back, set the previous value (for example `php81`) in `.env` and run the same `docker compose up` command.

## Database Configuration in PrestaShop Installation

When setting up PrestaShop, use the following database credentials:

- **Database server address:** `database` (for macOS) or `127.0.0.1` (on other systems)
- **Database name:** `docker`
- **Database login:** `root`
- **Database password:** `tiger`

Once entered, follow the PrestaShop installation steps to complete the setup.

## Contributing

We are happy if you want to create a pull request or help people with their issues. If you want to create a PR, please remember that this stack is not built for production usage, and changes should be good for general purpose and not overspecialized.

> Please note that we simplified the project structure from several branches for each php version, to one centralized master branch. Please create your PR against master branch.
>
> Thank you!

## Why you shouldn't use this stack unmodified in production

We want to empower developers to quickly create creative Applications. Therefore we are providing an easy to set up a local development environment for several different Frameworks and PHP Versions.
In Production you should modify at a minimum the following subjects:

- php handler: mod_php=> php-fpm
- secure mysql users with proper source IP limitations
