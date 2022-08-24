# Home Assistant log
This project initially started to get my Hive Thermostat working again but quickly grew into something much more.  

I have tried home assistant in the past but RPI computers are not powerful and in my opinion, are not fit for much else besides basic development. This time around, I used an old office computer and set out to make the next few weeks of my life incredibly difficult.  

This journal/log is mostly for my own notes and really stands as a quick reference for any pore soul that tries to attempt this the same way. 

# Hardware
The hardware I chose for this project was a small dell workstation computer with built-in wifi. (as I do not have a central I.T. Close) ethernet was not available where I needed it. The other key components I needed to accomplish my goals were a sonoff Zigbee radio and a Hive TH4 thermostat. 

# Journy
## Installing Debian
the basic install notes
1. Download and flash Debian to USB using Rufus
    - https://www.debian.org/distrib/netinst
    - https://rufus.ie/en/
1. Download the wifi driver and put it on another USB
    - https://github.com/OpenELEC/iwlwifi-firmware/tree/master/firmware
1. I un-checked the option to install a desktop interface, and made sure to have ssh enabled.

## Configure Debian to work well.
This includes adding better repos, installing Sudo, and configuring wifi
1. Configure non-free repos
    - https://dev.to/juniordevforlife/how-i-set-up-wifi-in-debian-11-14ka
1. Install sudo `apt install sudo`
1. Add user to the group of sudoers `usermod -aG sudo username`
1. Install iwd for wifi `apt install iwd`
1. Configured wifi
    - https://wiki.debian.org/WiFi/HowToUse#IWCtl
    ```bash
        systemctl --now disable wpa_supplicant
        systemctl --now enable iwd
        echo "[General]" >> /etc/iwd/main.conf
        ehco "EnableNetworkConfiguration=true" >> /etc/iwd/main.conf
    ```
    - https://wiki.archlinux.org/title/iwd#Connect_to_a_network
    ```bash
        device list
        station device scan
        station device get-networks
        station device connect SSID
        station device show
    ```
    - https://wiki.archlinux.org/title/iwd#Setting_static_IP_address_in_network_configuration
1. reboot `sudo reboot now`
1. check ip address `ip address`

## Installing Home Assistant
looking at all the options, It seemed like my only choice was home assistant supervised because I wanted ALL the features, but NOT on an RPI

- https://peyanski.com/how-to-install-home-assistant-supervised-official-way/#How_to_Install_Home_Assistant_Supervised
1. Install dependencies `sudo apt-get install jq wget curl avahi-daemon udisks2 libglib2.0-bin network-manager dbus apparmor -y`
1. double-check `sudo apt --fix-broken install`
1. Reboot `sudo reboot now`
1. Install Docker `curl -fsSL get.docker.com | sh`
1. check docker install `docker --version`
1. Download the latest release of Home Assistant OS `wget {latest release}`
    - https://github.com/home-assistant/os-agent/releases
1. Install Home Assistant OS `sudo dpkg -i {latest Release}.deb`
1. Verify Install `gdbus introspect --system --dest io.hass.os --object-path /io/hass/os`
1. Download HA supervisor `wget https://github.com/home-assistant/supervised-installer/releases/latest/download/homeassistant-supervised.deb`
1. Install the download `sudo dpkg -i homeassistant-supervised.deb`
1. go to the ip address and port 8123, home assistant should now be running.

## Get the Thermostat to work
Finally, after all that, I was ready to connect the thermostat to Home Assistant
1. Configured the Zigbee Dongle
1. Disconnected the Thermostat from power
1. Reconnected, and held the back + menu buttons till full reset.
1. Added Thermostat as Zigbee device in Home Assistant.

## Get Cloud to work.
1. Add the device Cloudflare to get DDNS configured.
1. Added the LetsEncrypt Plugin and used Cloudflare as the DNS provider.
1. Told home assistant to use TLS. 
    - I only used the configuration.yaml part of this guide, and ignored the API Part
    - https://www.home-assistant.io/blog/2015/12/13/setup-encryption-using-lets-encrypt/
1. Poort forwarded 8123 -> 443
1. Disabled Cloudflare proxy, this prevented HA from connecting properly.

## Configure Google Assistant
- https://www.home-assistant.io/integrations/google_assistant/
1. Follow the Guide
1. For the Configuration.yaml section, read the section about "Enable Device Sync" to get the service account JSON file.

# Conclusion
It took a lot of googling to get a decent home set up. it has a long way to go, but most of the base systems are now in place. Good Luck. 
