# How to add VPN software to the Pi to increase your privacy.

Note this uses OpenVPN and a (paid) VPN service (in this case Mullvad - recommended by 'That one guy' https://thatoneprivacysite.net/2017/10/03/mullvad-review/.
OpenVPN is used as a client to access the internet via a tunnel to the Mullvad server in your country or wherever you choose. These instructions are based on the ones on the Mullvad Site: https://www.mullvad.net/en/guides/linux-openvpn-installation/
Most VPN providers will give instructions to set up opne VPN.

This is different from setting up your own VPN server, which allows you to connect into your pi from the internet using the same tunneling process.

## First get the software

Download and install OpenVPN:

```
sudo apt-get update && apt-get upgrade
sudo apt-get install openvpn
```

Get your config files from your VPN provider:

In the case of mullvad, you log into your account on mullvad.net and download a zip file (mullvad_config_xx.zip).
Unzip the file ```unzip mullvad_config_xx.zip```
which contains four files (mullvad_ca.crt, mullvad_crl.pem, mullvad_xx.conf and mullvad_userpass.txt). 
These need to be copied across to the /etc/openvpn folder. Use:
```
sudo cp ~/Downloads/mullvad_ca.crt /etc/openvpn/mullvad_ca.crt 
```
for each file
