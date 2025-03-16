Self-Hosting Guide - Debian/Ubuntu server | Jitsi Meet
======================================================

Follow these steps for a quick Jitsi-Meet installation on a Debian-based GNU/Linux system. The following distributions are supported out-of-the-box:

*   Debian 10 (Buster) or newer
*   Ubuntu 22.04 (Jammy Jellyfish) or newer (Ubuntu 18.04 or 20.04 can be used, but Prosody must be updated to version 0.11+ before installation)

note

Many of the installation steps require `root` or `sudo` access. So it's recommended to have `sudo`/`root` access to your system.

Required packages and repository updates[​](#required-packages-and-repository-updates "Direct link to Required packages and repository updates")
------------------------------------------------------------------------------------------------------------------------------------------------

You will need the following packages:

*   `gnupg2`
*   `nginx-full`
*   `sudo` => **Only needed if you use `sudo`**
*   `curl` => **Or** `wget` **to [Add the Jitsi package repository](#add-the-jitsi-package-repository)**

Note

OpenJDK 11 must be used.

Make sure your system is up-to-date and required packages are installed:

Run as `root` or with `sudo`:

    # Retrieve the latest package versions across all repositoriessudo apt update# Ensure support for apt repositories served via HTTPSsudo apt install apt-transport-https

On Ubuntu systems, Jitsi requires dependencies from Ubuntu's `universe` package repository. To ensure this is enabled, run this command:

    sudo apt-add-repository universe

Retrieve the latest package versions across all repositories:

Install Jitsi Meet[​](#install-jitsi-meet "Direct link to Install Jitsi Meet")
------------------------------------------------------------------------------

### Domain of your server and set up DNS[​](#domain-of-your-server-and-set-up-dns "Direct link to Domain of your server and set up DNS")

Decide what domain your server will use. For example, `meet.example.org`.

Set a DNS A record for that domain, using:

*   your server's public IP address, if it has its own public IP; or
*   the public IP address of your router, if your server has a private (RFC1918) IP address (e.g. 192.168.1.2) and connects through your router via Network Address Translation (NAT).

If your computer/server or router has a dynamic IP address (the IP address changes constantly), you can use a dynamic dns-service instead. Example [DuckDNS](https://www.duckdns.org/).

DNS Record Example:

**Record Type**

**Hostname**

**Public IP**

**TTL (Seconds)**

`A`

`meet.example.org`

Your Meeting Server Public IP (`x.x.x.x`)

`1800`

### Set up the Fully Qualified Domain Name (FQDN) (optional)[​](#set-up-the-fully-qualified-domain-name-fqdn-optional "Direct link to Set up the Fully Qualified Domain Name (FQDN) (optional)")

If the machine used to host the Jitsi Meet instance has a FQDN (for example `meet.example.org`) already set up in DNS, you can set it with the following command:

    sudo hostnamectl set-hostname meet.example.org

Then add the same FQDN in the `/etc/hosts` file:

127.0.0.1 localhost x.x.x.x meet.example.org

note

`x.x.x.x` is your server's public IP address.

Finally on the same machine test that you can ping the FQDN with:

`ping "$(hostname)"`

If all worked as expected, you should see: `meet.example.org`

### Add the Prosody package repository[​](#add-the-prosody-package-repository "Direct link to Add the Prosody package repository")

This will add the Prosody repository so that an up to date Prosody is installed, which is necessary for features including the lobby feature.

**Ubuntu 18.04 and 20.04**

    echo deb http://packages.prosody.im/debian $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.listwget https://prosody.im/files/prosody-debian-packages.key -O- | sudo apt-key add -sudo apt install lua5.2

**Ubuntu 22.04**

    sudo curl -sL https://prosody.im/files/prosody-debian-packages.key -o /etc/apt/keyrings/prosody-debian-packages.keyecho "deb [signed-by=/etc/apt/keyrings/prosody-debian-packages.key] http://packages.prosody.im/debian $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/prosody-debian-packages.listsudo apt install lua5.2

### Add the Jitsi package repository[​](#add-the-jitsi-package-repository "Direct link to Add the Jitsi package repository")

This will add the jitsi repository to your package sources to make the Jitsi Meet packages available.

**Ubuntu 18.04 and 20.04**

    curl https://download.jitsi.org/jitsi-key.gpg.key | sudo sh -c 'gpg --dearmor > /usr/share/keyrings/jitsi-keyring.gpg'echo 'deb [signed-by=/usr/share/keyrings/jitsi-keyring.gpg] https://download.jitsi.org stable/' | sudo tee /etc/apt/sources.list.d/jitsi-stable.list > /dev/null

**Ubuntu 22.04**

    curl -sL https://download.jitsi.org/jitsi-key.gpg.key | sudo sh -c 'gpg --dearmor > /usr/share/keyrings/jitsi-keyring.gpg'echo "deb [signed-by=/usr/share/keyrings/jitsi-keyring.gpg] https://download.jitsi.org stable/" | sudo tee /etc/apt/sources.list.d/jitsi-stable.list

Update all package sources:

### Setup and configure your firewall[​](#setup-and-configure-your-firewall "Direct link to Setup and configure your firewall")

The following ports need to be open in your firewall, to allow traffic to the Jitsi Meet server:

*   `80 TCP` => For SSL certificate verification / renewal with Let's Encrypt. **Required**
*   `443 TCP` => For general access to Jitsi Meet. **Required**
*   `10000 UDP` => For General Network Audio/Video Meetings. **Required**
*   `22 TCP` => For Accessing your Server using SSH (change the port accordingly if it's not 22). **Required**
*   `3478 UDP` => For querying the stun server (coturn, optional, needs `config.js` change to enable it).
*   `5349 TCP` => For fallback network video/audio communications over TCP (when UDP is blocked for example), served by coturn. **Required**

If you are using `ufw`, you can use the following commands:

    sudo ufw allow 80/tcpsudo ufw allow 443/tcpsudo ufw allow 10000/udpsudo ufw allow 22/tcpsudo ufw allow 3478/udpsudo ufw allow 5349/tcpsudo ufw enable

Check the firewall status with:

#### Using SSH[​](#using-ssh "Direct link to Using SSH")

For more details on using and hardening SSH access, see the corresponding [Debian](https://wiki.debian.org/SSH) or [Ubuntu](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring) documentation.

#### Forward ports via your router[​](#forward-ports-via-your-router "Direct link to Forward ports via your router")

If you are running Jitsi Meet on a server [behind NAT](https://jitsi.github.io/handbook/docs/faq#how-to-tell-if-my-server-instance-is-behind-nat), forward the ports on your router to your server's IP address.

_Note_: if participants cannot see or hear each other, double check your firewall / NAT rules.

### TLS Certificate[​](#tls-certificate "Direct link to TLS Certificate")

In order to have encrypted communications, you need a [TLS certificate](https://en.wikipedia.org/wiki/Transport_Layer_Security).

During installation of Jitsi Meet you can choose between different options:

1.  The recommended option is to choose Let's Encrypt Certificate option
    
2.  But if you want to use a different certificate you should get that certificate first and then install jitsi-meet and choose _**I want to use my own certificate**_.
    
3.  You could also use the self-signed certificate(_**Generate a new self-signed certificate**_) but this is not recommended for the following reasons:
    
    *   Using a self-signed certificate will result in warnings being shown in your users browsers, because they cannot verify your server's identity.
        
    *   Jitsi Meet mobile apps _require_ a valid certificate signed by a trusted [Certificate Authority](https://en.wikipedia.org/wiki/Certificate_authority) and will not be able to connect to your server if you choose a self-signed certificate.
        

### Install Jitsi Meet[​](#install-jitsi-meet-1 "Direct link to Install Jitsi Meet")

_Note_: The installer will check if [Nginx](https://nginx.org/) or [Apache](https://httpd.apache.org/) are present (in that order) and configure a virtual host within the web server it finds to serve Jitsi Meet.

If you are already running Nginx on port 443 on the same machine, turnserver configuration will be skipped as it will conflict with your current port 443.

    # jitsi-meet installationsudo apt install jitsi-meet

**SSL/TLS certificate generation:** You will be asked about SSL/TLS certificate generation. See [above](#tls-certificate) for details.

**Hostname:** You will also be asked to enter the hostname of the Jitsi Meet instance. If you have a domain, use the specific domain name, for example: `meet.example.org`. Alternatively you can enter the IP address of the machine (if it is static or doesn't change).

This hostname will be used for virtualhost configuration inside Jitsi Meet and also, you and your correspondents will be using it to access the web conferences.

### Access Control[​](#access-control "Direct link to Access Control")

**Jitsi Meet server:** _Note_: By default, anyone who has access to your Jitsi Meet server will be able to start a conference: if your server is open to the world, anyone can have a chat with anyone else. If you want to limit the ability to start a conference to registered users, follow the instructions to set up a [secure domain](https://jitsi.github.io/handbook/docs/devops-guide/secure-domain/).

**Conferences/Rooms:** The access control for conferences/rooms is managed in the rooms, you can set a password on the webpage of the specific room after creation. See the User Guide for details: [https://jitsi.github.io/handbook/docs/user-guide/user-guide-start-a-jitsi-meeting](https://jitsi.github.io/handbook/docs/user-guide/user-guide-start-a-jitsi-meeting)

#### Advanced configuration[​](#advanced-configuration "Direct link to Advanced configuration")

If the installation is on a machine [behind NAT](https://jitsi.github.io/handbook/docs/faq#how-to-tell-if-my-server-instance-is-behind-nat) jitsi-videobridge should configure itself automatically on boot. If three way calls do not work, further configuration of jitsi-videobridge is needed in order for it to be accessible from outside.

Provided that all required ports are routed (forwarded) to the machine that it runs on. By default these ports are TCP/443 and UDP/10000.

Add a static mapping to the `ice4j.harvest.mapping` section in `/etc/jitsi/videobridge/jvb.conf`:

    ice4j {  harvest {    mapping {      static-mappings = [        {          local-address = "<Local.IP.Address>"          public-address = "<Public.IP.Address>"        }      ]    }  }}

See [the documentation of ice4j](https://github.com/jitsi/ice4j/blob/4f1329607cdcfd9ea13c0a5e7e099205775f7a0b/src/main/resources/reference.conf#L91) for details.

**Systemd/Limits:** Default deployments will have low values for maximum processes and open files. For greater than 100 participants, change `/etc/systemd/system.conf` to:

    DefaultLimitNOFILE=65000DefaultLimitNPROC=65000DefaultTasksMax=65000

To check values just run:

    systemctl show --property DefaultLimitNPROCsystemctl show --property DefaultLimitNOFILEsystemctl show --property DefaultTasksMax

To load the values and check them see [below](#systemd-details) for details.

##### Systemd details[​](#systemd-details "Direct link to Systemd details")

To reload the systemd changes on a running system execute `sudo systemctl daemon-reload` and `sudo systemctl restart jitsi-videobridge2`. To check the tasks part execute `sudo systemctl status jitsi-videobridge2` and you should see `Tasks: XX (limit: 65000)`. To check the files and process part execute ``cat /proc/`cat /var/run/jitsi-videobridge/jitsi-videobridge.pid`/limits`` and you should see:

    Max processes             65000                65000                processesMax open files            65000                65000                files

### Confirm that your installation is working[​](#confirm-that-your-installation-is-working "Direct link to Confirm that your installation is working")

Launch a web browser (such as Firefox, Chrome or Safari) and enter the hostname or IP address from the previous step into the address bar.

If you used a self-signed certificate (as opposed to using Let's Encrypt), your web browser will ask you to confirm that you trust the certificate. If you are testing from the iOS or Android app, it will probably fail at this point, if you are using a self-signed certificate.

You should see a web page prompting you to create a new meeting.  
Make sure that you can successfully create a meeting and that other participants are able to join the session.

If this all worked, then congratulations! You have an operational Jitsi conference service.

Uninstall[​](#uninstall "Direct link to Uninstall")
---------------------------------------------------

    sudo apt purge jigasi jitsi-meet jitsi-meet-web-config jitsi-meet-prosody jitsi-meet-turnserver jitsi-meet-web jicofo jitsi-videobridge2

Sometimes the following packages will fail to uninstall properly:

*   jigasi
*   jitsi-videobridge

When this happens, just run the uninstall command a second time and it should be ok.

The reason for the failure is that sometimes the uninstall script is faster than the process that stops the daemons. The second run of the uninstall command fixes this, as by then the jigasi or jitsi-videobridge daemons are already stopped.

Debugging problems[​](#debugging-problems "Direct link to Debugging problems")
------------------------------------------------------------------------------

*   Web Browser: You can try to use a different web browser. Some versions of some browsers are known to have issues with Jitsi Meet.
    
*   WebRTC, Webcam and Microphone: You can also visit [https://webrtc.github.io/samples/src/content/getusermedia/gum](https://webrtc.github.io/samples/src/content/getusermedia/gum) to test your browser's [WebRTC](https://en.wikipedia.org/wiki/WebRTC) support.
    
*   Firewall: If participants cannot see or hear each other, double check your firewall / NAT rules.
    
*   Nginx/Apache: As we prefer the usage of Nginx as webserver, the installer checks first for the presence of Nginx and then for Apache. In case you desperately need to enforce the usage of apache, try pre-setting the variable `jitsi-meet/enforce_apache` for package `jitsi-meet-web-config` on debconf.
    
*   Log files: Take a look at the various log files:
    

    /var/log/jitsi/jvb.log/var/log/jitsi/jicofo.log/var/log/prosody/prosody.log

Additional Functions[​](#additional-functions "Direct link to Additional Functions")
------------------------------------------------------------------------------------

### Adding sip-gateway to Jitsi Meet[​](#adding-sip-gateway-to-jitsi-meet "Direct link to Adding sip-gateway to Jitsi Meet")

#### Install Jigasi[​](#install-jigasi "Direct link to Install Jigasi")

Jigasi is a server-side application acting as a gateway to Jitsi Meet conferences. It allows regular [SIP](https://en.wikipedia.org/wiki/Session_Initiation_Protocol) clients to join meetings and provides transcription capabilities.

During the installation, you will be asked to enter your SIP account and password. This account will be used to invite the other SIP participants.

#### Reload Jitsi Meet[​](#reload-jitsi-meet "Direct link to Reload Jitsi Meet")

Launch again a browser with the Jitsi Meet URL and you'll see a telephone icon on the right end of the toolbar. Use it to invite SIP accounts to join the current conference.

Enjoy!