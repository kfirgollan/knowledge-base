# DNS

## Adding custom NS server using resolvconf

1. Install the package (`sudo apt-get install resolvconf`)
2. Edit the base configuration file (`/etc/resolvconf/resolv.conf.d/head`) by adding `nameserver <ip>` entries.
3. Regenerate `/etc/resolv.conf` file by running `sudo resolvconf -u`

Based on https://askubuntu.com/questions/130452/how-do-i-add-a-dns-server-via-resolv-conf
