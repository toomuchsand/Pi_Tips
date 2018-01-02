#Set up your Raspberry Pi as an access point.

There are a number of guides on how to do this out there, but many won't work as the way DCHP is handled in Raspbian has changed.
My first few attempts to do this were only a partial success. One thing to be clear on is whether you want to set up an access point that lets you connect to your internet connection via the access point, or if you just want an access point to a standalone local net. Many of the guides are for the latter, being written with setting up a local IoT net for example.
The guide I found that works is here: https://github.com/SurferTim/documentation/blob/master/configuration/wireless/access-point.md#using-the-raspberry-pi-as-an-access-point-to-share-an-internet-connection

Even this guide is a little confusing if you want to set up access to your internet as it starts off describing how to set up for a standalone network.

To make it really simple, I have copied just the bits required to set up and bridge your WIFI access point to your internet connection.

I started from the position of having a working ethernet connection and a working WIFI connection to my home network, with internet access possible via both.


#Set up the bridge
A 'bridge' needs to put in place between the wireless device and the Ethernet device on the access point Raspberry Pi. This bridge will pass all traffic between the two interfaces. Install the following packages to enable the access point setup and bridging.

sudo apt-get install hostapd bridge-utils
Since the configuration files are not ready yet, turn the new software off as follows:

sudo systemctl stop hostapd
Bridging creates a higher-level construct over the two ports being bridged. It is the bridge that is the network device, so we need to stop the eth0 and wlan0 ports being allocated IP addresses by the DHCP client on the Raspberry Pi.

sudo nano /etc/dhcpcd.conf
Add denyinterfaces wlan0 and denyinterfaces eth0 to the end of the file (but above any other added interface lines) and save the file.

Add a new bridge, which in this case is called br0.

sudo brctl addbr br0
Connect the network ports. In this case, connect eth0 to wlan0.

sudo brctl addif br0 eth0 wlan0
Now the interfaces file needs to be edited to adjust the various devices to work with bridging. sudo nano /etc/network/interfaces make the following edits.

Change the wlan entry to manual if it not already so, and remove any other entries e.g. any static address.

allow-hotplug wlan0
iface wlan0 inet manual
Add the bridging information at the end of the file.

# Bridge setup
auto br0
iface br0 inet dhcp
bridge_ports eth0 wlan0
The access point setup is almost the same as that shown in the previous section. Follow the instructions above to set up the hostapd.conf file, but add bridge=br0 below the interface=wlan0 line, and remove or comment out the driver line.

interface=wlan0
bridge=br0
#driver=nl80211
...
Now reboot the Raspberry Pi.

There should now be a functioning bridge between the wireless LAN and the Ethernet connection on the Raspberry Pi, and any device associated with the Raspberry Pi access point will act as if it is connected to the access point's wired Ethernet.

The ifconfig command will show the bridge, which will have been allocated an IP address via the wired Ethernet's DHCP server. The wlan0 and eth0 no longer have IP addresses, as they are now controlled by the bridge. It is possible to use a static IP address for the bridge if required, but generally, if the Raspberry Pi access point is connected to a ADSL router, the DHCP address will be fine.
