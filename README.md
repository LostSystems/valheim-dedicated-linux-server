# Valheim-Dedicated-Linux-Server

I've found the guide on Valheim Fandom doesn't really work well. So after a lot of trial and error this is what worked for me.

This guide is tailored for install within the /home/user directory and runs in screen, modify the scripts as needed for your specific install. Certain areas will be marked "user directory", "valheim server directory" and "user" which will need to be filled in based on your install.

**RUN THESE AT ROOT LEVEL**

apt install screen -y

dpkg --add-architecture i386

apt-get update

## Systemd script to start server on boot:

nano /etc/systemd/system/valheim.service
```
[Unit]
Description=Valheim Server
After=network-online.target

[Service]
Type=forking
User=<user> #Do not run the server in root for security reasons.
KillSignal=SIGINT
ExecStart=/usr/bin/screen -mdS valheimserver /home/user directory/valheim server directory/start_server.sh

[Install]
WantedBy=multi-user.target
```

chmod +x /path/to/valheim_server.x86_64

systemctl enable valheim

systemctl daemon-reload

## Start script within the installed Valheim Server Directory:

**Password for server needs to be minimum 5 characters**

nano /path/to/start_server.sh
```
#!/bin/bash
export templdpath=$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=./linux64:$LD_LIBRARY_PATH
export SteamAppId=892970

echo "Starting server PRESS CTRL-C to exit"

# Tip: Make a local copy of this script to avoid it being overwritten by steam.
# NOTE: Minimum password length is 5 characters & Password cant be in the server name.
# NOTE: You need to make sure the ports 2456-2458 is being forwarded to your server through your local router & firewall.
steamcmd +force_install_dir /home/user directory/valheim server directory +login anonymous +app_update 896660 +quit
/home/user directory/valheim server directory/valheim_server.x86_64 -batchmode -nographics -name "server name" -port port -world "world name" -password "password" -public 1
export LD_LIBRARY_PATH=$templdpath
```

Install dependencies:
```
apt install libpulse-dev libpulse-dev:i386 libpulse0 libpulse0:i386 libatomic1 libatomic1:i386 libc6 libc6:i386 lib32stdc++6 libncurses5 libncurses5:i386 lib32z1 libbz2-1.0:i386 lib32gcc-s1 libtinfo5 libtinfo5:i386 libcurl3-gnutls libcurl3-gnutls:i386 libsdl2-2.0-0 libsdl2-2.0-0:i386 -y
```
(Not all are needed for Valheim, but if using steamcmd for other servers this will help solve dependencies)

**RUN THESE AT USER LEVEL**

cd /home/<user directory>

mkdir ~/.steam/sdk64

cp /home/user directory/valheim server directory/linux64/steamclient.so /home/user directory/.steam/sdk64

./home/user directory/valheim server directory/start_server.sh

The initial install and configuration of the server will take place after this, once server is done initializing, Ctrl+C and reboot. Run "screen -ls" to check if the systemd script worked and the server is running. To see the server starting up in screen, run "screen -r [pid]" and to leave screen, Ctrl+A then D.

## Port Forwarding:

Ports 2456-2458 will need to be forwarded for friends and others to connect, that will be done in your router's config page and based on router model. If you are using ufw (strongly suggested) then configure your rules as needed.
