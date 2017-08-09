# multistreamer-installer

An install script for installing [Multistreamer](https://github.com/jprjr/multistreamer)
onto a server.

So far, this is only tested with Ubuntu 16.04.

This will:

1. Download ffmpeg, and dependencies for compiling OpenResty
2. Download/Compile/Install OpenResty + the nginx RTMP module and Stream-Lua modules
3. Install a precompiled version of [sockexec](https://github.com/jprjr/sockexec)
4. Download [htpasswd-auth-server](https://github.com/jprjr/htpasswd-auth-server)
5. Download [Multistreamer](https://github.com/jprjr/multistreamer)
6. Install needed lua modules for htpasswd-auth-server/multistreamer
7. Create scripts under /usr/local/bin for launching htpasswd-auth-server, multistreamer, and sockexec
8. Create a multistreamer user
9. Create systemd service units for htpasswd-auth-server, multistreamer, and sockexec

Optionally, it can:

1. Install Postgresql and redis and configure them
2. Install nginx + haproxy as reverse proxies
3. Setup dehydrated for automatic renewel of Let's Encrypt certificates
4. Create a basic configuration for multistreamer
5. Add your first user to htpasswd-auth-server

There's no ability to pick and choose among the optional steps. The default `./install`
script is what you should use if integrating multistreamer into an existing server.

`./setup` is meant for doing a brand-new install on a brand-new server.

## Things to do after running `./install`

When done, you'll have everything installed under `/opt`:

* `/opt/sockexec`
* `/opt/htpasswd-auth-server`
* `/opt/multistreamer`

When setting up `htpasswd-auth-server`, instead of typing long commands like:

* `./bin/htpasswd-auth-server -l /opt/openresty-rtmp/luajit/bin/luajit add`
* `./bin/htpasswd-auth-server -l /opt/openresty-rtmp/luajit/bin/luajit run`

you can simply call

* `htpasswd-auth-server add`
* `htpasswd-auth-server run`

Similarly for multistreamer, instead of typing:

* `./bin/multistreamer -l /opt/openresty-rtmp/luajit/bin/luajit -e prod initdb`
* `./bin/multistreamer -l /opt/openresty-rtmp/luajit/bin/luajit -e prod run`

you can just type

* `multistreamer -e prod initdb`
* `multistreamer -e prod run`

Basically, the `-l /opt/openresty-rtmp/luajit/bin/luajit` part of the command isn't
needed if you just type `multistreamer` instead of `./bin/multistreamer`, `htpasswd-auth-server`
instead of `./bin/htpasswd-auth-server`

Once you've finished setting up htpasswd-auth, multistreamer, etc manually and you're
sure everything is to your liking, enable and start the services:

```
systemctl enable sockexec.service htpasswd-auth-server.service multistreamer.service
systemctl start sockexec.service htpasswd-auth-server.service multistreamer.service
```

## Things to do after running the optional `./setup` script

Nothing, you'll have Let's Encrypt certificates installed, your first user created, etc.

Check out Dehydrated, at /opt/dehydrated, and its config at `/etc/dehydrated`. That's
where all the certificates, etc are.

A script is installed to `/etc/cron.daily/` to auto-renew Let's Encrypt certificates and
reload nginx + haproxy.

nginx is configured as a reverse proxy in front of Multistreamer's web interface,
Haproxy is used to provide SSL encryption for Multistreamer's IRC interface.

To create more users, just run `htpasswd-auth-server add`, you'll be prompted for a username
and password.

## Usage

Clone this repo somewhere, and run `install` as root:

```
git clone https://github.com/jprjr/multistreamer-installer
cd multistreamer
sudo ./install
```

If you want to do the optional setup, run `setup`

## License

Released under an MIT license, see `LICENSE`
