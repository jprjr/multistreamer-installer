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

It *does not*:

1. Install Postgresql or redis
2. Do any configuration of multistreamer, htpasswd-auth-server, etc

## Things to do after running this script

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

## Usage

Clone this repo somewhere, and run `install` as root:

```
git clone https://github.com/jprjr/multistreamer-installer
cd multistreamer
sudo ./install
```

## License

Released under an MIT license, see `LICENSE`
