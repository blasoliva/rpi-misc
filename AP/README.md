#### Wireless AP configuration for Raspberry Pi
Required packages
~~~
# IEEE 802.11 AP, IEEE 802.1X/WPA/WPA2/EAP/RADIUS Authenticator
apt-get install hostapd

# DHCP server
apt-get install isc-dhcp-server
~~~

Configure hostapd (/etc/hostapd/hostapd.conf) 
~~~
interface=wlan0
hw_mode=g
driver=nl80211
ssid=pi-server
channel=1
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=3
wpa_passphrase=S3cur3W1r3l355P455w0rd
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
wmm_enabled=0
~~~
Configure DHCP server (/etc/dhcp/dhcpd.conf)
~~~
subnet 10.0.0.0 netmask 255.255.255.0 {
  range 10.0.0.10 10.0.0.20;
  option broadcast-address 10.0.0.255;
  option routers 10.0.0.1;
  default-lease-time 600;
  max-lease-time 7200;
  option domain-name "local";
  option domain-name-servers 10.0.0.1, 8.8.8.8, 8.8.4.4;
}
~~~
Enable network interface to start as a daemon (/etc/default/isc-dhcp-server)
~~~
DHCPD_CONF=/etc/dhcp/dhcpd.conf
INTERFACES="wlan0"
~~~

Configure network interfaces settings (/etc/network/interfaces)
~~~
auto lo
iface lo inet loopback

iface eth0 inet static
  address 192.168.1.8
  netmask 255.255.255.0
  broadcast 192.168.1.255
  gateway 192.168.1.10

auto wlan0
iface wlan0 inet static
  address 10.0.0.1
  netmask 255.255.255.0
  ~~~
**Note**: I use this configuration to create a wireless network for a local application without internet access. Only eth0 interface has a gateway. So if you want to have an internet access on wlan0 you should configure the interface on your network range or figure out how to make a route between interfaces. 
