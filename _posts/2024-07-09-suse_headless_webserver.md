# building a gigabyte brix suse distro

1. download latest copy of suse [tumbleweed](https://get.opensuse.org/tumbleweed)

2. burn this iso image to thumbrive

   ```sh
   dd if=<location_of_iso> of=/dev/rdisk<thumbdrive_disk_number> bs-1m
   ```

3. put this in the front usb slot of the brix

4. power off and power on (a hard reboot is important)

5. press delete key repeatedly to get brix bios

6. change boot order in boot section to specify usb drive as 1st boot
7. Save and Exit (reboot on thumbdrive)
8. run installer

## enable ssh

*(if it's not installed)*

```sh
sudo zypper refresh
sudo zypper install --no-confirm openssh

sudo systemctl start sshd
sudo systemctl enable sshd
```

### firewall

#### allow ssh

```sh
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

#### allow web

```sh
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

## configuring grub for headless boot

edit `/etc/default/grub`

change `GRUB_TERMINAL="gfxterm"` to `GRUB_TERMINAL="console"`

run

```sh
grub2-mkconfig -o /boot/grub2/grub.cfg
```

## Apache

### installing apache

```sh
zypper in apache2
```

### configuring apache

- document root can be found in `/etc/apache2/default-server.conf` and by default is `/srv/www/htdocs`

- add your websites as separate folders to this location eg. `mkdir -p /srv/www/htdocs/mywebsite`

   by default apache denies access to all files. Don't edit the http.conf file correctly, rather create a default virtual host file in `vhosts.d` directory that sends all traffic to the default location (`svr/www/htdocs/mywebsite`) - if in future you add multiple sites then this is the file to edit and add new sites here.

  the way I did it was to `cd` to `/etc/apache2/vhosts.d` and

  ```sh
  cp vhost.template _default_vhost.conf
  ```

  (`vhost.conf` files are loaded automatically in name order so the `_default_vhost.conf` file name virtually guarentees it will be the first one )

  I then edited the `_default_vhost.conf` and changed the placeholders for the domain and the document root for my domain and my document root of `/svr/www/htdocs/mywebsite`

  be sure to scan the whole file for any other paths you could insert `/svr/www/htdocs/mywebsite` as there are a couple

  then restart apache with

  ```sh
  systemctl restart apache2
  ```

This worked for me
