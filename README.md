# Cloud Config

This contains the default configuration for my normal server.
Using [cloud-init](https://cloudinit.readthedocs.io/en/latest/reference/modules.html) and the install scripts, it should be fairly fast to spin up a new server.


``` sh-session
Create new server in Hetzner Cloud Console with cloud-init config.
$ TODO: Use hcloud to automate new server creation on hetzner.

# Connect via ssh
$ ssh flori@serienabend.duckdns.org -p 4444

# Create logwatch cache folder
$ sudo mkdir -p /var/cache/logwatch

# Get scripts and data
$ git clone -b neko --single-branch https://github.com/Fidge123/cloud-init.git --depth 1
$ cd cloud-init

# Decrypt secrets using key in password manager
$ gpg -d neko.tgz.gpg | tar xz
$ rm neko/._*
$ mv neko/{.,}* .
$ rmdir neko

# Configure restic
$ sudo su
$ source restic.env 
$ restic init # if necessary
$ restic restore latest
$ restic backup --files-from restic-files.txt

# Set mail PW (extra space is intentional)
$  sudo sed -i -e "s/SMTP_PW/$SMTP2GO/g" /root/.msmtprc
$  sudo sed -i -e "s/SMTP_PW/$SMTP2GO/g" /home/flori/.msmtprc
$ exit

# Create acme.json if necessary
$ touch acme.json
$ sudo chmod 600 acme.json

# Run docker
$ docker compose up -d
```

For debugging these logs are helpful:
- `/var/log/cloud-init.log`: The actual process logs for cloud-init's processing of the configuration files.
- `/var/log/cloud-init-output.log`: Any output produced by the processing of the configuration can be found here.

## Check after initial deployment

- Check NTP status: `sudo ntpq -p`
- Check fail2ban status: `sudo fail2ban-client status`
- Check open ports: `sudo ss -lntup`
- Check msmtp: `echo "hello, world!" | msmtp flori@hey.com`

## Secrets

Create secrets: `tar -cz neko/{.,}* | gpg -c -o neko.tgz.gpg`

Decrypt: `gpg -d neko.tgz.gpg | tar xz`
