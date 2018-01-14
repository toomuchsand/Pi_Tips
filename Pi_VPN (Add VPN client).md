# How to add VPN software to the Pi to increase your privacy.

Note this uses OpenVPN and a (paid) VPN service (in this case Mullvad - recommended by 'That one guy' https://thatoneprivacysite.net/2017/10/03/mullvad-review/).

OpenVPN is used as a client to access the internet via an encrypted tunnel to the Mullvad server in your country or wherever you choose. These instructions are based on the ones on the Mullvad Site: https://www.mullvad.net/en/guides/linux-openvpn-installation/
Most half-decent VPN providers will give instructions to set up OpenVPN.

Note: This is different from setting up your own VPN server, which would allow you to connect into your pi from other devices the internet using the same tunneling process. Your internet traffic is secure between you and your Pi, but 'visible' from you to your ISP.

## First get the software

Download and install OpenVPN:

```
sudo apt-get update && apt-get upgrade
sudo apt-get install openvpn
```

## Then get your config file(s)
Get your config files from your VPN provider:

These may be different depending on provider. A lot of providers give you an ```.ovpn``` config file, but in the case of mullvad, you log into your account on mullvad.net and download a zip file (mullvad_config_xx.zip).
Unzip the file ```unzip mullvad_config_xx.zip```
which contains four files (mullvad_ca.crt, mullvad_crl.pem, mullvad_xx.conf and mullvad_userpass.txt). 
These need to be copied across to the /etc/openvpn folder. Use:
```
sudo cp ~/Downloads/mullvad_ca.crt /etc/openvpn/mullvad_ca.crt 
```
for each file (be careful typing the file names!)

## Run OpenVPN

Then run openvpn as a service:
```sudo service openvpn start```

You can use ```top``` to check that openvpn is running.

Also use https://am.i.mullvad.net to check you are connected, or a service like https://www.iplocation.net to check that your ip has changed from your ISPs IP to the VPN provider IP.

Also have a look at ```ifconfig```, you should see a ```tun0``` entry as well as your ```eth0``` or ```wlan0``` interfaces.

## Why Privacy?
If you are wondering why go to all this trouble? Please read: https://www.mullvad.net/en/blog/2016/12/5/privacy-universal-right/.

