# How to set up your Pi as an 'aways on' torrent machine.

This guide based on a combination of https://www.makeuseof.com/tag/how-to-turn-your-raspberry-pi-into-an-always-on-downloading-megalith/
and
https://github.com/qbittorrent/qBittorrent/wiki/Setting-up-qBittorrent-on-Ubuntu-server-as-daemon-with-Web-interface-(15.04-and-newer)

## Why BitTorrent
Torrents have an bad reputation as a means of sharing copyright info, but they do have legitimate uses. For example, The Raspberry Pi Raspbian Operating system is available via a torrent:
https://downloads.raspberrypi.org/raspbian_latest.torrent

## Set up sequence
This guide will tak you through the following steps:
- Install Raspbian (if not already installed)
- Set your Pi to run from the USB drive
- Set up a folder on the USB to store your torrents
- Install VPN client (optional)
- Install a BitTorrent client
- Install a file sharing system (optional)
