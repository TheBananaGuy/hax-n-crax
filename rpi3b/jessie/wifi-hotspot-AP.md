# Pi hotspot

## Prerequisites

- Raspbian Jessie
- Internet connection via an ethernet cable
- An ethernet cable
- A USB Wi-Fi adapter
- Open terminal on your rPi through SSH connection or a desktop GUI / in a desktop environment via HDMI cable

## Set up a direct wired connection

Initiate super user, open up the DHCP client configuration

```
sudo su
sudo nano /etc/dhcpcd.conf
```

Assuming your normal router's IP is 192.168.0.1 on your network, add the following to the end of the file:

```
interface eth0
static ip_address=192.168.0.199/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1
```

Save, confirm, reboot.

## Download hostapd and tweak the interfaces

Update the Pi, download software, open up interface configuration

```
sudo apt-get update
sudo apt-get install hostapd bridge-utils
sudo nano /etc/network/interfaces
```

To prevent Raspbian Jessie from interfering with the Wi-Fi interface, do the following:
- find the block that starts with ```allow-hotplug wlan0```
- comment out every line of this and the following blocks

The result should look like this:

```
...

auto eth0
allow-hotplug eth0
iface eth0 inet manual

#auto wlan0
#allow-hotplug wlan0
#iface wlan0 inet manual
#	wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

#allow-hotplug wlan1
#iface wlan1 inet manual
#	wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

#iface default inet dhcp
```

Save, confirm, reboot.

### Optional - bridge, pt I

You can add a bridge between the ethernet and Wi-Fi interfaces by adding the following at the end:

```
auto br0
iface br0 inet dhcp
bridge_ports eth0 wlan0
```

Save, confirm, reboot.

## Configure hostapd

Create a configuration file
```
sudo nano /etc/hostapd/hostapd.conf
```

Fill it with your options, where
- XX is your country code
- YYYYY is your network name
- ZZZZZZZZ is your network password

```
interface=wlan1
driver=nl80211
country_code=**
ssid=********
hw_mode=g
channel=6
auth_algs=1
wpa=2
wpa_passphrase=********
wpa_key_mgmt=WPA-PSK
ieee80211n=1
wmm_enabled=1
```

Save, confirm.

### Optional - bridge, pt II

If you've set up the bridge in the first optional part, add this line between **interface** and **country_code**

```
bridge=br0
```

Save, confirm.

### Dissection

```interface``` wireless interface for the access point | wlan0 for rPi3's own interface, wlan1 for external

```bridge``` bridge between ethernet and Wi-Fi interfaces

```country_code``` country code | 2 letters (???)

```ssid``` ssid or network name | letters, numbers, special characters (???)

```hw_mode``` ***

```channel``` ***

```auth_langs``` wpa2 encryption support | true/false

```wpa``` wpa encryption

```wpa_passphrase``` network password | min 8 characters; letters, numbers, special characters (???)

```wpa_key_mgmt``` ***

```ieee80211n``` *** | true/false

```wmm_enabled``` *** | true/false

## Using the hotspot

### Prerequisites

- A device to test the wireless hotspotx

### Foreground / testing

Activate the hotspot on the Pi
Deactivate by quitting the process (Ctrl+C)


```
sudo nano hostapd -d /etc/hostapd/hostapd.conf
```

If everything is in order, you should see the name of your hotspot on Wi-Fi connections list
If anything wentwrong, it will output an error

### Background / access point on boot

Open default configuration

```
sudo nano /etc/default/hostapd
```

Search for ```DAEMON_CONF=""```, uncomment it, and point it to your hostapd configuration file
The result should look like this:

```
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```

Save, confirm, reboot.

## Other

Suggested: If using an adapter like Edimax EW-7811Un, you need to replace the hostapd binary due to the Realtek RTL8188CUS chipset specifics
```
sudo wget http://files.raspiplace.com/hostapd-rt- -O /usr/bin/hostapd
```
The link doesn't work though...

### Bugs

- Using wlan0 results in an error which says that it does not support monitor mode
- Connection is resetting every 10 minutes

### Notes and Ideas

- External adapters provide better connection than rPi3's own
- Fix documentation
- Try different adapters
- Optimise startup configuration