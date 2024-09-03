# Cloud Config

This contains the default configuration for my normal server.
Using [cloud-init](https://cloudinit.readthedocs.io/en/latest/reference/modules.html) and the install scripts, it should be fairly fast to spin up a new server.


``` sh-session
Create new server in Hetzner Cloud Console with cloud-init config.
$ TODO: Use hcloud to automate new server creation on hetzner.

# Connect via ssh
$ ssh flori@IP-ADDRESS -p 4444

# Set mail PW (extra space is intentional)
$  sudo sed -i -e 's/SMTP_PW/NEW_PW/g' /root/.msmtprc
$  sudo sed -i -e 's/SMTP_PW/NEW_PW/g' /home/flori/.msmtprc

# Create logwatch cache folder
$ sudo mkdir /var/cache/logwatch

# Get scripts and data
$ git clone https://github.com/Fidge123/cloud-init.git
$ cd cloud-init

# Decrypt secrets using key in password manager
$ gpg -d secrets.tgz.gpg | tar xz

# Run postgres script (use install instead of restore if needed)
$ chmod +x secrets/restore_postgres.sh
$ ./secrets/restore_postgres.sh

# Check pgbackrest
$ sudo -u postgres pgbackrest --stanza=main --log-level-console=info check

# Configure restic
$ sudo su
$ source secrets/restic.env 
$ restic init
$ restic restore latest

# Run docker
$ docker compose up -d
```

For debugging these logs are helpful:
- `/var/log/cloud-init.log`: The actual process logs for cloud-init's processing of the configuration files.
- `/var/log/cloud-init-output.log`: Any output produced by the processing of the configuration can be found here.

## Check after initial deployment

- Check NTP status: `sudo ntpq -p`
- Check fail2ban status: `sudo fail2ban-client status`
- Check AIDE: `sudo aide.wrapper --check` (accept changes `sudo aideinit -y -f`)
- Check open ports: `sudo ss -lntup`
- Check msmtp: `echo "hello, world!" | msmtp flori@hey.com`

## Secrets

Create secrets: `tar -cz secrets/ | gpg -c -o secrets.tgz.gpg`

Decrypt: `gpg -d secrets.tgz.gpg | tar xz`

## Services

### Docker
- [Nginx Proxy Manager](https://nginxproxymanager.com): Expose your services easily and securely (Port 81)
  - https://npm.flori-richter.de
  - Default credentials: admin@example.com / changeme
- [Stirling PDF](https://github.com/Stirling-Tools/Stirling-PDF): Perform various operations on PDF files (Port 8080)
  - https://pdf.flori-richter.de
  - It is necessary to set `client_max_body_size 100M;` so large PDFs are also handled
  - TODO: Protect with authelia
- [Miniflux](https://miniflux.app): A minimalist and opinionated feed reader (Port 8080)
  - https://rss.flori-richter.de
- [IT-Tools](https://github.com/CorentinTh/it-tools): Collection of handy online tools for developers (Port 80)
  - https://tools.flori-richter.de
  - TODO: Protect with authelia
- [PGAdmin](https://www.pgadmin.org): Administration and development platform for PostgreSQL (Port 80)
  - https://pgadmin.flori-richter.de
- [Rallly](https://support.rallly.co/self-hosting/introduction): A tool for creating scheduling polls (Port 3000)
  - https://rallly.flori-richter.de
  - TODO: Connect with Authelia OIDC
- [Authelia](https://www.authelia.com/): An authentication and authorization server and portal (Port 9091)
  - https://auth.flori-richter.de
- [Umami](https://umami.is/docs): Simple, fast, privacy-focused analytics solution (Port 3000)
  - https://analytics.flori-richter.de
  - Default credentials: admin / umami

### Local
- [PostgreSQL](https://www.postgresql.org): A powerful, open source object-relational database system (Port 5432)
  - postgres://pg.flori-richter.de:5432 (only local connections)
  - Tune performance: https://bun.uptrace.dev/postgres/performance-tuning.html
  - TODO: SSL
- [pgBackRest](https://pgbackrest.org): Reliable PostgreSQL Backup & Restore
- [restic](https://restic.net): Restic is a modern backup program
  - TODO: Logging
- [fail2ban](https://www.fail2ban.org): Daemon to ban hosts that cause multiple authentication errors
  - Protect PSQL: https://serverfault.com/questions/627169/how-to-secure-an-open-postgresql-port
- [msmtp](https://wiki.debian.org/msmtp): An SMTP client that can be used to send mails
- [logwatch](https://ubuntu.com/server/docs/how-to-install-and-configure-logwatch): Keeps an eye on your logs for you, flags items that may be of interest, and reports them via email

## My apps

- Resume / CV
  - https://flori-richter.de -> https://cv.flori-richter.de/en
  - TODO: Create small nginx docker container (https://static-web-server.net/features/docker/)
- Tippspiel App
  - https://nfl-tippspiel.de
  - TODO: Dockerize, migrate email service?
- Tippspiel Logos
  - https://nfl-tippspiel.de/logos
  - TODO: Dockerize
- Tippspiel Updater
  - TODO: Dockerize
- Discord Bot
  - https://vtt.flori-richter.de
  - TODO: Dockerize
