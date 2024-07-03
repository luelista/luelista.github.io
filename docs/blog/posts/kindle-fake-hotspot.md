---
date: 2024-07-03
---

# how to put shit on old kindle without USB and internet

my kindle's USB port is broken, so I can't just connect via USB to put books on it

it also refuses to connect to WiFi hotspots without a backing internet connection. however, I don't want to connect it to the internet because it will phone home to amazon and potentially do auto-updates i don't want.

but, enshittification of the internet to the rescue, the kindle accepts a wifi hotspot with a "captive portal", so we pretend to be one.

## prerequisites
Python, NetworkManager, dnsmasq, Calibre

make sure firewall allows connections from wifi to local dns, dhcp, port 80 and whatever port is configured in calibre (default 8080)

## make nm connection config
nmtui, "edit connection", "add", type "wifi", select wifi card as device, mode "access point", security "wpa & wpa2 personal", enter some password, ipv4 configuration "manual", add address "10.42.0.1/24", disable "automatically connect"

<details><summary>Networkmanager profile</summary>

``` collapse title="Networkmanager profile"
===============================================================================
                     Connection profile details (Hotspot)
===============================================================================
connection.id:                          Hotspot
connection.uuid:                        10e9bbfa-7d25-4525-9da4-8f424e9e0836
connection.stable-id:                   --
connection.type:                        802-11-wireless
connection.interface-name:              wlp2s0
connection.autoconnect:                 no
connection.autoconnect-priority:        0
connection.autoconnect-retries:         -1 (default)
connection.multi-connect:               0 (default)
connection.auth-retries:                -1
connection.timestamp:                   1720017613
connection.permissions:                 --
connection.zone:                        --
connection.controller:                  --
connection.master:                      --
connection.slave-type:                  --
connection.port-type:                   --
connection.autoconnect-slaves:          -1 (default)
connection.autoconnect-ports:           -1 (default)
connection.down-on-poweroff:            -1 (default)
connection.secondaries:                 --
connection.gateway-ping-timeout:        0
connection.metered:                     unknown
connection.lldp:                        default
connection.mdns:                        -1 (default)
connection.llmnr:                       -1 (default)
connection.dns-over-tls:                -1 (default)
connection.mptcp-flags:                 0x0 (default)
connection.wait-device-timeout:         -1
connection.wait-activation-delay:       -1
-------------------------------------------------------------------------------
802-11-wireless.ssid:                   amazon_sucks
802-11-wireless.mode:                   ap
802-11-wireless.band:                   --
802-11-wireless.channel:                0
802-11-wireless.bssid:                  --
802-11-wireless.mac-address:            --
802-11-wireless.cloned-mac-address:     --
802-11-wireless.generate-mac-address-mask:--
802-11-wireless.mac-address-denylist:   --
802-11-wireless.mac-address-randomization:default
802-11-wireless.mtu:                    auto
802-11-wireless.hidden:                 no
802-11-wireless.powersave:              0 (default)
802-11-wireless.wake-on-wlan:           0x1 (default)
802-11-wireless.ap-isolation:           -1 (default)
-------------------------------------------------------------------------------
802-11-wireless-security.key-mgmt:      wpa-psk
802-11-wireless-security.wep-tx-keyidx: 0
802-11-wireless-security.auth-alg:      --
802-11-wireless-security.proto:         rsn
802-11-wireless-security.pairwise:      ccmp
802-11-wireless-security.group:         ccmp
802-11-wireless-security.pmf:           0 (default)
802-11-wireless-security.leap-username: --
802-11-wireless-security.wep-key0:      <hidden>
802-11-wireless-security.wep-key1:      <hidden>
802-11-wireless-security.wep-key2:      <hidden>
802-11-wireless-security.wep-key3:      <hidden>
802-11-wireless-security.wep-key-flags: 0 (none)
802-11-wireless-security.wep-key-type:  unknown
802-11-wireless-security.psk:           <hidden>
802-11-wireless-security.psk-flags:     0 (none)
802-11-wireless-security.leap-password: <hidden>
802-11-wireless-security.leap-password-flags:0 (none)
802-11-wireless-security.wps-method:    0x0 (default)
802-11-wireless-security.fils:          0 (default)
-------------------------------------------------------------------------------
ipv4.method:                            manual
ipv4.dns:                               --
ipv4.dns-search:                        --
ipv4.dns-options:                       --
ipv4.dns-priority:                      0
ipv4.addresses:                         10.42.0.1/24
ipv4.gateway:                           --
ipv4.routes:                            --
ipv4.route-metric:                      -1
ipv4.route-table:                       0 (unspec)
ipv4.routing-rules:                     --
ipv4.replace-local-rule:                -1 (default)
ipv4.dhcp-send-release:                 -1 (default)
ipv4.ignore-auto-routes:                no
ipv4.ignore-auto-dns:                   no
ipv4.dhcp-client-id:                    --
ipv4.dhcp-iaid:                         --
ipv4.dhcp-dscp:                         --
ipv4.dhcp-timeout:                      0 (default)
ipv4.dhcp-send-hostname:                yes
ipv4.dhcp-hostname:                     --
ipv4.dhcp-fqdn:                         --
ipv4.dhcp-hostname-flags:               0x0 (none)
ipv4.never-default:                     no
ipv4.may-fail:                          yes
ipv4.required-timeout:                  -1 (default)
ipv4.dad-timeout:                       -1 (default)
ipv4.dhcp-vendor-class-identifier:      --
ipv4.link-local:                        0 (default)
ipv4.dhcp-reject-servers:               --
ipv4.auto-route-ext-gw:                 -1 (default)
```
</details>

## run with it

- run in terminal
  ```shell
  # start hotspot
  nmcli connection up Hotspot
  
  # dns/dhcp server to pretend our offline hotspot is a somewhat working wifi
  sudo dnsmasq --conf-file=/dev/null --no-hosts --keep-in-foreground --bind-interfaces --except-interface=lo --clear-on-reload --strict-order --listen-address=10.42.0.1 --dhcp-range=10.42.0.10,10.42.0.254,60m  -q --log-dhcp \
    -8 /dev/fd/2 --no-resolv \
    --auth-server=dns.kindle.com,wlp2s0 \
    --host-record=dns.kindle.com,10.42.0.1  --auth-zone=dns.kindle.com,10.42.0.0/24 \
    --host-record=dogvgb9ujhybx.cloudfront.net,10.42.0.1 --auth-zone=cloudfront.net,10.42.0.0/24 \
    --host-record=spectrum.s3.amazonaws.com,10.42.0.1 --auth-zone=s3.amazonaws.com,10.42.0.0/24
  
  
  # pretend we're in a captive portal...
  # run in empty folder:
  sudo python -m http.server 80
  ```
- run calibre "content server"
- connect to your hotspot on the kindle
- click "yes" when it asks whether you want to connect to the captive portal

![nonbinary drake meme / ✋ am regnerischen tag im urlaub entspannt bücher lesen / 👉 am regnerischen tag im urlaub stundenlang gegen die ranzige amazon-software auf einem alten kindle mit kaputtem microUSB-port, networkmanager, und dnsmasq kämpfen, um da bücher draufzukopieren](https://github.com/luelista/luelista.github.io/assets/388142/2a1dec79-2007-4674-bbb7-0e825ee8220b)

- navigate to 10.42.0.1:8080 in the web browser
- download books from calibre to the kindle
- 🎉
