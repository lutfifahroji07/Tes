/interface ethernet
set [ find default-name=ether1 ] disable-running-check=no name=ether1-WAN
set [ find default-name=ether2 ] comment="LOCAL PROXMOX" \
    disable-running-check=no
set [ find default-name=ether3 ] disable-running-check=no name=\
    ether3-LOCAL-NAT
/ip pool
add name=VPN-POOL-NAT ranges=192.168.123.200-192.168.123.250
/ppp profile
add local-address=192.168.123.200 name=L2TP-PROFILE remote-address=\
    VPN-POOL-NAT
/interface l2tp-server server
set default-profile=L2TP-PROFILE enabled=yes max-sessions=10 use-ipsec=yes
/ip address
add address=202.46.85.198/30 interface=ether1-WAN network=202.46.85.196
add address=192.168.124.1/24 interface=ether2 network=192.168.124.0
add address=192.168.123.1/24 interface=ether3-LOCAL-NAT network=192.168.123.0
/ip dhcp-client
add interface=ether1-WAN
/ip dns
set servers=8.8.8.8
/ip firewall nat
add action=masquerade chain=srcnat src-address=192.168.123.0/24
/ip firewall service-port
set ftp disabled=yes
set tftp disabled=yes
set h323 disabled=yes
set sip disabled=yes
set pptp disabled=yes
/ip route
add disabled=no dst-address=0.0.0.0/0 gateway=202.46.85.197 routing-table=\
    main suppress-hw-offload=no
/ip service
set ftp disabled=yes
set telnet disabled=yes
set www disabled=yes
set api disabled=yes
set api-ssl disabled=yes
set winbox port=9216
set ssh port=20002
/ppp secret
add name=vpnuser profile=L2TP-PROFILE service=l2tp
/system identity
set name=Mikrotik-CHR.CRK
/system ntp client
set enabled=yes
/system ntp client servers
add address=id.pool.ntp.org
