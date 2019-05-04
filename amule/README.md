# Configuration for Libreelec and Openelec

Libreelec is just a very cool thing: it boots much faster than Raspian when you power on your Raspberry Pi.<br>
Why not plugging some external drives and get it running the aMule daemon in silence instead of keeping your PC 24h/24 running noisily when the fan needs to cool the processor or the power unit ?<br>
This is the purpose of this image.<br><br>

Enable the docker add_on on your Kodi host, and enjoy.

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

To use web ui from an Internet browser or amulegui (as remote):

```sh
docker run --name "amule" \
-p 4711:4711 -p 4712:4712 -p 8004:8004 -p 8006:8006/udp -p 8007:8007/udp \
-e PUID=[wanted_uid] -e PGID=[wanted_gid]  \
-e WEBUI_PWD=[wanted_password_for_web_interface] \
-e GUI_PWD=[wanted_password_for_gui_interface] \
-v /storage/amule/conf:/home/amule/.aMule -v /storage/amule/incoming:/incoming -v /storage/amule/tmp:/temp synopsis8/raspberrypi3-amule

```
### Explanation of the command line parameters
- docker run : run the image "synopsis8/raspberrypi3-amule" and give the name "amule" to the container.
- publish the following ports "outside" the docker container, so they're available to the Raspberry Pi and our local network (otherwise they're not accessible, it's like a virtual machine where the ports needs to be mapped in the NAT)
- setup the userid and groupid to be used to write the files. On Libreelec, the user is "root" so you can use '0' as PUID and '0' again as 'PGID'
- Set the password for the WEBUI
- Set the password for the GUI (in case you prefer to use the GUI, which is asctualy faster than the WEBUI). You can use both actually.
- Map the aMule folders as they are mounted on your Raspberry once you have plugged your external drives with the aMule folders as they appear inside the docker container


- Then point your browser to http://libreelec-host:4711
- or if you have aMule installed on your PC and prefer to use the amulegui instead, call amulegui on your PC, and connect your libreelec-host on port 4712.

<u>Note:</u>
You can run this command as many times you want, but remember one thing: the amule.sh script within the image will check for the presence of a amule.conf file beforehand. If a amule.conf files does already exist (because you imported one old configuration), it will not get overwritten, therefore: the passwords you are enterinng in the command line or the aMule folder configuration as they're mounted on your Raspberry will not be updated.<br>
This case, you'll have either to remove the amule.conf file to generate a new one, or edit it manually.

## Security
This image download a fresh version of an ipfilter.dat file at the start. I recommend you to check what the purpose of ipfilter.dat file is.

## Start as a service
Now you might want to have it running automatically when your Raspberry PI is powered on. Create the file /storage/.config/system.d/amule.service with the following contents.

```sh
[Unit]
Description=amule Container
Requires=service.system.docker.service
After=service.system.docker.service network.target
Before=kodi.service

[Service]
Restart=no
RestartSec=10s
TimeoutStartSec=0
ExecStart=/storage/.kodi/addons/service.system.docker/bin/docker container start "amule"

[Install]
WantedBy=multi-user.target
```

Now, execute: 
```sh
systemctl enable amule
```

aMule will then automatically start as service

https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=VKUUYX4BCAJQG&source=url

