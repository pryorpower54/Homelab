# Cisco C1111-8P — Sanitized Running Configuration

> All sensitive values replaced with placeholders.

```
version 16.6
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
!
hostname C1111-HOME
!
enable secret <ENABLE-SECRET>
!
no aaa new-model
!
ip domain-name homelab.local
!
ip ddns update method NO-IP
 HTTP
  add http://<NOIP-USER>:<NOIP-KEY>@dynupdate.no-ip.com/nic/update?hostname=<h>&myip=<a>
 interval maximum 0 1 0 0
!
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.99.1 192.168.99.10
!
ip dhcp pool Lab
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8 8.8.4.4
!
ip dhcp pool Pentest
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8 8.8.4.4
!
ip dhcp pool IoT
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8 8.8.4.4
!
ip dhcp pool Management
 network 192.168.99.0 255.255.255.0
 default-router 192.168.99.1
 dns-server 8.8.8.8 8.8.4.4
!
username admin privilege 15 secret <ADMIN-SECRET>
!
crypto ikev2 proposal IKEV2-PROPOSAL
 encryption aes-cbc-256
 integrity sha256
 group 14
!
crypto ikev2 policy IKEV2-POLICY
 proposal IKEV2-PROPOSAL
!
crypto ikev2 keyring IKEV2-KEYRING
 peer ANY
  address 0.0.0.0 0.0.0.0
  pre-shared-key <VPN-PSK>
!
crypto ikev2 profile IKEV2-PROFILE
 match identity remote any
 authentication remote pre-share
 authentication local pre-share
 keyring local IKEV2-KEYRING
 dpd 30 5 periodic
 virtual-template 1
!
crypto ipsec transform-set IPSEC-TS esp-aes 256 esp-sha256-hmac
 mode tunnel
!
crypto ipsec profile IPSEC-PROFILE
 set transform-set IPSEC-TS
 set ikev2-profile IKEV2-PROFILE
!
vlan internal allocation policy ascending
!
class-map type inspect match-any CM-VPN-TRAFFIC
 match access-group name ACL-VPN
class-map type inspect match-any CM-LAB-TRAFFIC
 match protocol tcp
 match protocol udp
 match protocol icmp
class-map type inspect match-any CM-INTERNET-TRAFFIC
 match protocol tcp
 match protocol udp
 match protocol icmp
!
policy-map type inspect PM-WAN-TO-SELF
 class type inspect CM-VPN-TRAFFIC
  inspect
 class class-default
  drop
policy-map type inspect PM-LAB-TO-LAB
 class type inspect CM-LAB-TRAFFIC
  inspect
 class class-default
  drop
policy-map type inspect PM-IOT-TO-WAN
 class type inspect CM-INTERNET-TRAFFIC
  inspect
 class class-default
  drop
policy-map type inspect PM-DROP-ALL
 class class-default
  drop
policy-map type inspect PM-LAB-TO-WAN
 class type inspect CM-INTERNET-TRAFFIC
  inspect
 class class-default
  drop
!
zone security WAN
 description Untrusted Internet
zone security Lab
 description Trusted Lab Network
zone security Pentest
 description Isolated Pentest Network
zone security IoT
 description Low Trust IoT Network
zone security Management
 description Trusted Management Network
!
zone-pair security ZP-IOT-TO-WAN source IoT destination WAN
 service-policy type inspect PM-IOT-TO-WAN
zone-pair security ZP-LAB-TO-MGMT source Lab destination Management
 service-policy type inspect PM-LAB-TO-LAB
zone-pair security ZP-LAB-TO-WAN source Lab destination WAN
 service-policy type inspect PM-LAB-TO-WAN
zone-pair security ZP-MGMT-TO-LAB source Management destination Lab
 service-policy type inspect PM-LAB-TO-LAB
zone-pair security ZP-MGMT-TO-WAN source Management destination WAN
 service-policy type inspect PM-LAB-TO-WAN
zone-pair security ZP-PENTEST-TO-WAN source Pentest destination WAN
 service-policy type inspect PM-IOT-TO-WAN
zone-pair security ZP-SELF-TO-WAN source self destination WAN
 service-policy type inspect PM-LAB-TO-WAN
zone-pair security ZP-WAN-TO-IOT source WAN destination IoT
 service-policy type inspect PM-DROP-ALL
zone-pair security ZP-WAN-TO-LAB source WAN destination Lab
 service-policy type inspect PM-DROP-ALL
zone-pair security ZP-WAN-TO-MGMT source WAN destination Management
 service-policy type inspect PM-DROP-ALL
zone-pair security ZP-WAN-TO-PENTEST source WAN destination Pentest
 service-policy type inspect PM-DROP-ALL
zone-pair security ZP-WAN-TO-SELF source WAN destination self
 service-policy type inspect PM-WAN-TO-SELF
!
interface GigabitEthernet0/0/0
 description WAN-Spectrum
 ip ddns update hostname <YOUR-DDNS-HOSTNAME>
 ip ddns update NO-IP
 ip address <WAN-IP> 255.255.255.0
 ip nat outside
 zone-member security WAN
 negotiation auto
!
interface GigabitEthernet0/0/1
 no ip address
 negotiation auto
!
interface GigabitEthernet0/1/0
 description Lab
 switchport access vlan 10
 switchport mode access
!
interface GigabitEthernet0/1/1
 description Lab
 switchport access vlan 10
 switchport mode access
!
interface GigabitEthernet0/1/2
 description Lab
 switchport access vlan 10
 switchport mode access
!
interface GigabitEthernet0/1/3
 description Pentest
 switchport access vlan 20
 switchport mode access
!
interface GigabitEthernet0/1/4
 description Pentest
 switchport access vlan 20
 switchport mode access
!
interface GigabitEthernet0/1/5
 description IoT
 switchport access vlan 30
 switchport mode access
!
interface GigabitEthernet0/1/6
 description IoT
 switchport access vlan 30
 switchport mode access
!
interface GigabitEthernet0/1/7
 description Management
 switchport access vlan 99
 switchport mode access
!
interface Virtual-Template1 type tunnel
 ip unnumbered GigabitEthernet0/0/0
 ip nat inside
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC-PROFILE
!
interface Vlan10
 description Lab-Gateway
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
 zone-member security Lab
!
interface Vlan20
 description Pentest-Gateway
 ip address 192.168.20.1 255.255.255.0
 ip nat inside
 zone-member security Pentest
!
interface Vlan30
 description IoT-Gateway
 ip address 192.168.30.1 255.255.255.0
 ip nat inside
 zone-member security IoT
!
interface Vlan99
 description Management-Gateway
 ip address 192.168.99.1 255.255.255.0
 ip nat inside
 zone-member security Management
!
ip nat inside source list LAB-NAT interface GigabitEthernet0/0/0 overload
ip route 0.0.0.0 0.0.0.0 192.168.1.1
ip ssh time-out 60
ip ssh version 2
ip ssh authentication-retries 3
!
ip access-list standard LAB-NAT
 permit 192.168.10.0 0.0.0.255
 permit 192.168.20.0 0.0.0.255
 permit 192.168.30.0 0.0.0.255
 permit 192.168.99.0 0.0.0.255
!
ip access-list extended ACL-VPN
 permit udp any any eq isakmp
 permit udp any any eq non500-isakmp
 permit esp any any
 permit tcp any any eq 22
 permit tcp <UPSTREAM-SUBNET> 0.0.0.255 any eq 22
 permit icmp <UPSTREAM-SUBNET> 0.0.0.255 any
!
line con 0
 transport input none
 stopbits 1
line vty 0 4
 login local
 transport input ssh
 exec-timeout 10 0
!
end
```
