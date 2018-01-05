# Set up your Raspberry Pi as an access point.
There are a number of guides on how to set up the Pi as an Access Point (AP) out there, but many won't work as the way DCHP is handled in Raspbian has been changed slighty with the release of Stretch.

As a result my first few attempts to do this were only a partial success. I could set up the access point, but in doing so, killed my internet connection. DHCP was throwing up errors. 

One thing to be clear on is whether you want to set up an access point that lets you connect to your internet connection via the access point, or if you just want an access point to a standalone local net. Many of the guides on the net are for the latter, being written with setting up a local IoT net, but no connection to the 'outside world' for example.

The guide I found that works is here: https://github.com/SurferTim/documentation/blob/master/configuration/wireless/access-point.md#using-the-raspberry-pi-as-an-access-point-to-share-an-internet-connection (now adopted as the official Raspberry Pi documentation: https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md#internet-sharing)

Even the above guide is a little confusing if you want to set up access to your internet as it starts off describing how to set up for a standalone network.

To make it really simple, I have copied just the bits required to set up and bridge your WIFI access point to your internet connection. There are actually fewer steps to configure for internet access as your internet router/modem does the DHCP for you, while if you set up a standalone net you have to set up and configure a DCHP server.

I started from the position of having a working ethernet connection and a working WIFI connection to my home network, with internet access possible via both.


## Get the software
A 'bridge' needs to be put in place between the wireless device and the Ethernet device on the access point Raspberry Pi. This bridge will pass all traffic between the two interfaces. Install the following packages to enable the access point setup and bridging. You are installing an accesspoint software called hostapd and a package of bridge software called bridge-utils, you will use brctl to set up the beidge. You can type 
```man hostpd```
or 
```man brctl```
to find out a bit more.

```
sudo apt-get install hostapd bridge-utils
```

## Stop hostapd from running
Since the configuration files are not ready yet, turn the hostapd software off as follows:

```
sudo systemctl stop hostapd
```
Bridging creates a higher-level construct over the two ports being bridged. It is the bridge that is the network device, so we need to stop the eth0 and wlan0 ports being allocated IP addresses by the DHCP client on the Raspberry Pi.

## Edit the DHCP config file
```
sudo nano /etc/dhcpcd.conf
```
Add 
```denyinterfaces wlan0``` 
and 
```denyinterfaces eth0```
to the end of the file (but above any other added interface lines) and save the file.

## Tell the system about the bridge
A new bridge, which in this case is called br0.

```sudo brctl addbr br0```
Connect the network ports. In this case, connect eth0 to wlan0.

```sudo brctl addif br0 eth0 wlan0```

## Edit the interfaces file
Now the interfaces file needs to be edited to adjust the various devices to work with bridging. 
```sudo nano /etc/network/interfaces``` 
make the following edits.

Change the wlan entry to manual if it not already so, and remove any other entries e.g. any static address.
```
allow-hotplug wlan0
iface wlan0 inet manual
```
Add the bridging information at the end of the file.

```
auto br0
iface br0 inet dhcp
bridge_ports eth0 wlan0
```

## Configuring the access point host software (hostapd)

You need to edit the hostapd configuration file, located at /etc/hostapd/hostapd.conf, to add the various parameters for your wireless network. After initial install, this will be a new/empty file.

```
sudo nano /etc/hostapd/hostapd.conf
```

Add the information below to the configuration file. This configuration assumes we are using channel 7, with a network name of NameOfNetwork, and a password AardvarkBadgerHedgehog. Note that the name and password should **not** have quotes around them. 

```
interface=wlan0
bridge=br0
#driver=nl80211
ssid=NameOfNetwork
hw_mode=g
channel=7
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=AardvarkBadgerHedgehog
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```
Note that the 'driver=' line is commented out. This worked for me, but you may need to leave it on, or find the right driver if your WIFI dongle is unusual or wasn't working before you started. I think its fair to say if your WIFI dongle let you connect to your existing WIFI network before you decided to switch it to an access point, then all is well with your driver.

We now need to tell the system where to find this configuration file.

```
sudo nano /etc/default/hostapd
```

Find the line with #DAEMON_CONF, and replace it with this:

```
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```



...
## Now reboot the Raspberry Pi.

There should now be a functioning bridge between the wireless LAN and the Ethernet connection on the Raspberry Pi, and any device associated with the Raspberry Pi access point will act as if it is connected to the access point's wired Ethernet.

The ifconfig command will show the bridge, which will have been allocated an IP address via the wired Ethernet's DHCP server. The wlan0 and eth0 no longer have IP addresses, as they are now controlled by the bridge. It is possible to use a static IP address for the bridge if required, but generally, if the Raspberry Pi access point is connected to a ADSL router, the DHCP address will be fine.
