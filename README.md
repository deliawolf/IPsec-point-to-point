# IPsec-point-to-point

same like https://github.com/deliawolf/Static-VTI-Point-to-Point-Tunnels . but less explanation only configuration.

IPsec Point to Point (R1 to R2) Tunnel IP R1 to R2 (172.16.34.4/28 to 172.16.34.3/28)

1. Create an ISAKMP Policy on Both Routers
  - ISAKMP authentication type: pre-shared key
  - ISAKMP hash: SHA 512
  - ISAKMP encryption algorithm: AES 256
  - ISAKMP lifetime: 2 hours
  - ISAKMP DH group: 15

R1:
```
R1(config)#crypto isakmp policy 1
R1(config-isakmp)#authentication pre-share 
R1(config-isakmp)#hash sha512 
R1(config-isakmp)#encryption aes 256
R1(config-isakmp)#lifetime 7200
R1(config-isakmp)#group 15
```
R2:
```
R2(config)#crypto isakmp policy 1
R2(config-isakmp)#authentication pre-share 
R2(config-isakmp)#hash sha512 
R2(config-isakmp)#encryption aes 256
R2(config-isakmp)#lifetime 7200
R2(config-isakmp)#group 15
```
2. Create Pre-shared Key
  - ISAKMP pre-shared key: cisco123 (the address is ip from other end. R1 using R2 ip as destination and vise versa)

R1:
```
R1(config)#crypto isakmp key cisco123 address 172.16.23.3
```
R2:
```
R2(config)#crypto isakmp key cisco123 address 172.16.24.4
```
3. Define the IPSec Transform Set
  - IPsec authentication: ESP SHA512
  - IPsec encryption: ESP AES 256
R1:
```
R1(config)#crypto ipsec transform-set MYSET esp-aes 256 esp-sha512-hmac
```
R2:
```
R2(config)#crypto ipsec transform-set MYSET esp-aes 256 esp-sha512-hmac
```
4. Define IPsec profile and include IPSec Transform Set in the profile
R1:
```
R1(config)#crypto ipsec profile MYPROFILE
R1(ipsec-profile)#set transform-set MYSET
```
R2:
```
R2(config)#crypto ipsec profile MYPROFILE
R2(ipsec-profile)#set transform-set MYSET
```
4. Define the tunnel and include the protection profile
R1:
```
R1(config)#interface tunnel 0
R1(config-if)#ip address 172.16.34.4 255.255.255.240
R1(config-if)#tunnel source ethernet 0/0
R1(config-if)#tunnel destination 172.16.23.3
R1(config-if)#tunnel mode ipsec ipv4
R1(config-if)#tunnel protection ipsec profile MYPROFILE 
*Jan 19 10:01:33.813: %CRYPTO-6-ISAKMP_ON_OFF: ISAKMP is ON
```
R2:
```
R2(config)#interface tunnel 0
R2(config-if)#ip address 172.16.34.3 255.255.255.240
R2(config-if)#tunnel source ethernet 0/2
R2(config-if)#tunnel destination 172.16.24.4
R2(config-if)#tunnel mode ipsec ipv4
R2(config-if)#tunnel protection ipsec profile MYPROFILE 
*Jan 19 10:01:33.813: %CRYPTO-6-ISAKMP_ON_OFF: ISAKMP is ON
```
5. Verify config
```
show crypto ipsec sa
```
```
show interfaces tunnel 0
```
