# eap_proxy

Proxy EAP packets between interfaces on Ubiquiti Networks EdgeRouter™ products. Also works with UniFi® Security Gateway.

Inspired by 1x_prox as posted here:

<http://www.dslreports.com/forum/r30693618->

AT&T Residential Gateway Bypass - True bridge mode!

## IPV4 Instructions for Unifi USG

Please see <https://medium.com/@mrtcve/at-t-gigabit-fiber-modem-bypass-using-unifi-usg-updated-c628f7f458cf/>

1.  Clone repo
    * git clone https://github.com/uchagani/eap_proxy.git
2.  Back up Unifi Controller config
    * Navigate to your UniFi controller web page and go to Settings > Maintenance > Backup > Download Backup.
3.  Connnect the LAN port on the USG to your LAN (USG to Switch)
4.  Connect the cable from your ONT/Fiber Box to the USG WAN port.
5.  Connect the USG LAN2 port to the ONT port on your Uverse modem
6.  On the Unifi Controller go to Settings > Networks > Create New Network and enter the below options
    * Name: LAN2
    * Purpose: Corporate
    * Parent Interface: LAN2
    * Gateway/Subnet: 192.168.254.1/24 (or whatever you prefer)
    * DHCP Mode: None
7.  Save the new network
8.  On the Unifi Controller go to Settings > Networks > Edit (Next to WAN).
9.  Check “Use VLAN ID” and enter 0 in the box to the right
10.  Click Save
11.  Copy files to the Unifi USG via SCP:
```bash
scp -r /Users/uchagani/dev/eap_proxy/eap_proxy.sh unifi_username@192.168.1.1:/config/scripts/post-config.d`

scp -r /Users/uchagani/dev/eap_proxy/eap_proxy.py unifi_username@192.168.1.1:/config/scripts/post-config.d
```
12.  Move `eap_proxy.py`
```bash
sudo mv /config/scripts/post-config.d/eap_proxy.py /config/scripts/
```
13. Run `sudo python /config/scripts/eap_proxy.py --restart-dhcp --ignore-when-wan-up --ignore-logoff --ping-gateway --set-mac eth0 eth2`
14. Power cycle AT&T modem
15. After a 5 minutes you should start seeing packets flowing through. 
16. Visit an external site and make sure you have connection to the outside world.
17. If it's working proceed.  If not, troubleshoot by using the the directions here and links above.
18. Kill the command that we started above with `ctrl+c`
19. Make the `eap_proxy.sh` script executable:
```bash
cd /config/scripts/post-config.d
chmod +x eap_proxy.sh
```
20. Reboot the USG with `reboot now`
21. If  you don't have a network connection in 5min or so, reboot the AT&T modem again.  It usually takes 5min or so for everything to come back up and a connection made.
