# Configuration for Libreelec and Openelec

Create needed directories with

```sh
mkdir -p /storage/amule/conf
mkdir -p /storage/amule/tmp
mkdir -p /storage/amule/incoming
```

# Usage

Just run the container with the following command lines (replace brackets with the values you want to use) :

## Start from scratch

If you don't have any existing amule configuration, you can specify a custom password for GUI and / or WebUI using adequate environment variables.

The default ports are often blocked or restricted by some Internet Service Providers, they have been changed. Make sure to redirect your firewall ports to your Raspberry/LibreELEC host:

- 8004/tcp
- 8006/udp
- 8007/udp

- To use web ui from a browser :

```sh
docker run --name "amule" \
    -p 4711:4711 -p 8004:8004 -p 8006:8006/udp -p 8007:8007/udp \
    -e PUID=[wanted_uid] -e PGID=[wanted_gid] \
    -e WEBUI_PWD=[wanted_password_for_web_interface] \
    -v /storage/amule/conf:/home/amule/.aMule -v /storage/amule/incoming:/incoming -v /storage/amule/tmp:/temp synopsis8/raspberrypi3-amule
```

Then point your browser to http://libreelec-host:4711

## Security
This image download a fresh version of an ipfilter.dat file at the start. I recommend you to check what the purpose of ipfilter.dat file is.

## Start as a service
Now you might want to have it running automatically when your Raspberry PI is powered on. Create the file /storage/.config/system.d/amule.service with the following contents.

```sh
[Unit] Description=amule Container Requires=service.system.docker.service After=service.system.docker.service network.target Before=kodi.service

[Service] Restart=no RestartSec=10s TimeoutStartSec=0 ExecStart=/storage/.kodi/addons/service.system.docker/bin/docker container start "amule"

[Install] WantedBy=multi-user.target
```

Now, execute: ````sh systemctl enable amule```

aMule will then automatically start as service
