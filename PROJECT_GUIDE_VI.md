# Hướng dẫn triển khai Secure Enterprise Network trong Cisco Packet Tracer

Tài liệu này hướng dẫn xây dựng project theo từng mốc. Không chuyển sang mốc tiếp theo nếu mốc hiện tại chưa vượt qua kiểm tra.

## 1. Mục tiêu cuối cùng

Xây dựng mạng doanh nghiệp mô phỏng có:

- VLAN riêng cho IT, HR, Accounting, Employees, Servers, Guest và Management.
- Trunk giữa core switch và access switch.
- Inter-VLAN routing trên multilayer switch.
- DHCP và DNS tập trung trên Server-PT.
- Kết nối ISP giả lập và NAT/PAT trên edge router.
- ACL cô lập Guest, HR và Accounting.
- SSH chỉ cho phép từ VLAN IT.
- Port Security trên cổng người dùng.
- Tài liệu thiết kế, cấu hình, kiểm thử và ảnh bằng chứng trên GitHub.

## 2. Nguyên tắc thực hiện

1. Cấu hình từng lớp: physical -> Layer 2 -> Layer 3 -> services -> Internet -> security.
2. Sau mỗi thay đổi, kiểm tra ngay thay vì cấu hình toàn bộ rồi mới test.
3. Lưu file Packet Tracer theo phiên bản: `v01-topology.pkt`, `v02-vlan.pkt`, ...
4. Không dùng mật khẩu thật trong lab hoặc GitHub.
5. Không thêm ACL trước khi connectivity cơ bản hoạt động.
6. Chỉ ghi vào README những tính năng đã cấu hình và kiểm tra thành công.

## 3. Thiết bị cần kéo vào Packet Tracer

| Số lượng | Thiết bị | Tên đặt trong project |
|---:|---|---|
| 1 | Router 2911 | R1-EDGE |
| 1 | Router 2911 | R2-ISP |
| 1 | Multilayer Switch 3560-24PS | SW-CORE-L3 |
| 3 | Switch 2960-24TT | SW-ACCESS-01, SW-ACCESS-02, SW-ACCESS-03 |
| 1 | Server-PT | SRV-INTERNAL |
| 1 | Server-PT | SRV-PUBLIC |
| 2 | PC-PT | IT-PC-01, HR-PC-01 |
| 2 | PC-PT | ACC-PC-01, EMP-PC-01 |
| 1 | PC-PT | GUEST-PC-01 |

Một PC cho mỗi VLAN là đủ để hoàn thành bản đầu. Có thể thêm máy sau khi toàn bộ hệ thống hoạt động.

## 4. Sơ đồ kết nối cổng

Chọn biểu tượng dây tự động (Automatically Choose Connection Type) hoặc Copper Straight-Through.

| Thiết bị A | Cổng A | Thiết bị B | Cổng B | Loại kết nối |
|---|---|---|---|---|
| R2-ISP | GigabitEthernet0/0 | R1-EDGE | GigabitEthernet0/0 | WAN |
| R2-ISP | GigabitEthernet0/1 | SRV-PUBLIC | FastEthernet0 | Public network |
| R1-EDGE | GigabitEthernet0/1 | SW-CORE-L3 | GigabitEthernet0/1 | Routed link |
| SW-CORE-L3 | FastEthernet0/1 | SW-ACCESS-01 | GigabitEthernet0/1 | Trunk |
| SW-CORE-L3 | FastEthernet0/2 | SW-ACCESS-02 | GigabitEthernet0/1 | Trunk |
| SW-CORE-L3 | FastEthernet0/3 | SW-ACCESS-03 | GigabitEthernet0/1 | Trunk |
| SW-CORE-L3 | FastEthernet0/24 | SRV-INTERNAL | FastEthernet0 | VLAN 50 access |
| SW-ACCESS-01 | FastEthernet0/1 | IT-PC-01 | FastEthernet0 | VLAN 10 access |
| SW-ACCESS-01 | FastEthernet0/2 | HR-PC-01 | FastEthernet0 | VLAN 20 access |
| SW-ACCESS-02 | FastEthernet0/1 | ACC-PC-01 | FastEthernet0 | VLAN 30 access |
| SW-ACCESS-02 | FastEthernet0/2 | EMP-PC-01 | FastEthernet0 | VLAN 40 access |
| SW-ACCESS-03 | FastEthernet0/1 | GUEST-PC-01 | FastEthernet0 | VLAN 60 access |

Nếu model trong Packet Tracer có tên cổng khác, giữ nguyên vai trò kết nối và ghi lại cổng thực tế trong `docs/interface-map.md`.

## 5. Kế hoạch VLAN và IP

| VLAN | Tên | Mạng | Gateway/SVI | DHCP bắt đầu |
|---:|---|---|---|---|
| 10 | IT | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.100 |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.100 |
| 30 | ACCOUNTING | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.100 |
| 40 | EMPLOYEES | 192.168.40.0/24 | 192.168.40.1 | 192.168.40.100 |
| 50 | SERVERS | 192.168.50.0/24 | 192.168.50.1 | Không cấp động |
| 60 | GUEST | 192.168.60.0/24 | 192.168.60.1 | 192.168.60.100 |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 | Không cấp động |
| 999 | BLACKHOLE | Không có | Không có | Không có |

Các mạng liên kết:

| Kết nối | Thiết bị/IP thứ nhất | Thiết bị/IP thứ hai |
|---|---|---|
| Core ↔ Edge | R1-EDGE: 10.255.255.1/30 | SW-CORE-L3: 10.255.255.2/30 |
| Edge ↔ ISP | R2-ISP: 203.0.113.1/30 | R1-EDGE: 203.0.113.2/30 |
| Public network | R2-ISP: 198.51.100.1/24 | SRV-PUBLIC: 198.51.100.10/24 |

Địa chỉ server và switch management:

| Thiết bị | IP | Gateway |
|---|---|---|
| SRV-INTERNAL | 192.168.50.10/24 | 192.168.50.1 |
| SW-ACCESS-01 | 192.168.99.11/24 | 192.168.99.1 |
| SW-ACCESS-02 | 192.168.99.12/24 | 192.168.99.1 |
| SW-ACCESS-03 | 192.168.99.13/24 | 192.168.99.1 |
| SRV-PUBLIC | 198.51.100.10/24 | 198.51.100.1 |

## 6. Mốc 0 - Tạo repository từ đầu

### 6.1 Tạo repository trên GitHub

1. Đăng nhập GitHub.
2. Chọn **New repository**.
3. Repository name: `secure-enterprise-network-packet-tracer`.
4. Description: `A secure segmented enterprise network implemented in Cisco Packet Tracer using VLANs, routing, DHCP, DNS, NAT, ACLs, SSH and port security.`
5. Chọn **Public**.
6. Không chọn tạo README, `.gitignore` hoặc license trên GitHub vì bộ khung local đã có.
7. Chọn **Create repository**.

### 6.2 Đưa bộ khung local lên GitHub

Mở PowerShell tại thư mục project rồi chạy:

```powershell
git init
git branch -M main
git add .
git commit -m "docs: initialize project structure and implementation plan"
git remote add origin https://github.com/Whitepuil/secure-enterprise-network-packet-tracer.git
git push -u origin main
```

Nếu Git yêu cầu danh tính:

```powershell
git config --global user.name "Chau Thai Khang"
git config --global user.email "khang0911181204@gmail.com"
```

### 6.3 Điểm kiểm tra

- Repository mở được ở chế độ public.
- README xuất hiện ở trang chính.
- Các thư mục `docs`, `configs`, `packet-tracer`, `diagrams`, `screenshots` tồn tại.
- Commit đầu tiên có nội dung rõ ràng.

## 7. Mốc 1 - Dựng topology, chưa cấu hình

1. Mở Cisco Packet Tracer.
2. Kéo đúng thiết bị trong mục 3.
3. Đổi Display Name của từng thiết bị.
4. Sắp xếp ISP ở trên, edge router phía dưới, core ở giữa, access switch phía dưới core và PC ở cuối.
5. Nối dây theo bảng mục 4.
6. Dùng Place Note để ghi vai trò các đường kết nối.
7. Lưu file thành `packet-tracer/v01-topology.pkt`.
8. Chụp toàn bộ topology thành `diagrams/physical-topology.png`.

Commit:

```powershell
git add .
git commit -m "feat: create initial Packet Tracer topology"
git push
```

Điểm kiểm tra:

- Không có thiết bị thừa.
- Tên thiết bị khớp tài liệu.
- Cổng kết nối khớp bảng interface.
- File `.pkt` mở lại không lỗi.

## 8. Mốc 2 - Cấu hình cơ bản

Thực hiện trên mỗi router/switch, thay hostname tương ứng.

```text
enable
configure terminal
hostname SW-CORE-L3
no ip domain-lookup
service password-encryption
enable secret <LAB_ENABLE_SECRET>
banner motd #AUTHORIZED LAB ACCESS ONLY#
line console 0
 logging synchronous
 exec-timeout 10 0
exit
end
write memory
```

Giải thích:

- `no ip domain-lookup`: tránh thiết bị cố phân giải DNS khi gõ sai lệnh.
- `service password-encryption`: che các password dạng clear text cơ bản; không thay thế secret mạnh.
- `enable secret`: bảo vệ privileged EXEC mode.
- `logging synchronous`: log không làm đứt dòng đang gõ.
- `exec-timeout`: tự đóng console khi không sử dụng.

Kiểm tra:

```text
show running-config | include hostname
show running-config | include enable secret
```

Lưu `v02-base-config.pkt` và commit:

```text
chore: apply baseline device configuration
```

## 9. Mốc 3 - VLAN, access port và trunk

### 9.1 Tạo VLAN trên SW-CORE-L3

```text
enable
configure terminal
vlan 10
 name IT
vlan 20
 name HR
vlan 30
 name ACCOUNTING
vlan 40
 name EMPLOYEES
vlan 50
 name SERVERS
vlan 60
 name GUEST
vlan 99
 name MANAGEMENT
vlan 999
 name BLACKHOLE
exit
```

Tạo cùng danh sách VLAN trên ba access switch.

### 9.2 Cấu hình trunk trên core

```text
interface range fastEthernet0/1 - 3
 description TRUNK_TO_ACCESS_SWITCH
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 no shutdown
exit
```

Một số model không chấp nhận lệnh encapsulation; không thêm `switchport trunk encapsulation dot1q` nếu thiết bị không hỗ trợ.

### 9.3 Trunk trên mỗi access switch

```text
interface gigabitEthernet0/1
 description TRUNK_TO_CORE
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 no shutdown
exit
```

### 9.4 Gán access port

SW-ACCESS-01:

```text
interface fastEthernet0/1
 description IT-PC-01
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown
exit
interface fastEthernet0/2
 description HR-PC-01
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
exit
```

SW-ACCESS-02:

```text
interface fastEthernet0/1
 description ACC-PC-01
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown
exit
interface fastEthernet0/2
 description EMP-PC-01
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 no shutdown
exit
```

SW-ACCESS-03:

```text
interface fastEthernet0/1
 description GUEST-PC-01
 switchport mode access
 switchport access vlan 60
 spanning-tree portfast
 no shutdown
exit
```

Server port trên core:

```text
interface fastEthernet0/24
 description SRV-INTERNAL
 switchport mode access
 switchport access vlan 50
 spanning-tree portfast
 no shutdown
exit
```

### 9.5 Kiểm tra Layer 2

```text
show vlan brief
show interfaces trunk
show interfaces status
```

Phải thấy:

- PC port nằm đúng VLAN.
- Fa0/1-3 trên core là trunk.
- Gi0/1 trên access switch là trunk.
- VLAN 10-60, 99 và 999 tồn tại.

Lưu `v03-vlan-trunk.pkt` và commit:

```text
feat: configure VLANs access ports and trunk links
```

## 10. Mốc 4 - Inter-VLAN routing

### 10.1 Tạo SVI trên SW-CORE-L3

```text
enable
configure terminal
ip routing
interface vlan 10
 description GW_IT
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.50.10
 no shutdown
interface vlan 20
 description GW_HR
 ip address 192.168.20.1 255.255.255.0
 ip helper-address 192.168.50.10
 no shutdown
interface vlan 30
 description GW_ACCOUNTING
 ip address 192.168.30.1 255.255.255.0
 ip helper-address 192.168.50.10
 no shutdown
interface vlan 40
 description GW_EMPLOYEES
 ip address 192.168.40.1 255.255.255.0
 ip helper-address 192.168.50.10
 no shutdown
interface vlan 50
 description GW_SERVERS
 ip address 192.168.50.1 255.255.255.0
 no shutdown
interface vlan 60
 description GW_GUEST
 ip address 192.168.60.1 255.255.255.0
 ip helper-address 192.168.50.10
 no shutdown
interface vlan 99
 description GW_MANAGEMENT
 ip address 192.168.99.1 255.255.255.0
 no shutdown
end
write memory
```

SVI chỉ lên trạng thái up/up khi VLAN tồn tại và có ít nhất một port/trunk active mang VLAN đó.

### 10.2 Cấu hình management IP cho access switch

SW-ACCESS-01:

```text
configure terminal
interface vlan 99
 ip address 192.168.99.11 255.255.255.0
 no shutdown
exit
ip default-gateway 192.168.99.1
end
write memory
```

Làm tương tự với `.12` và `.13` cho SW-ACCESS-02 và SW-ACCESS-03.

### 10.3 Gán IP tĩnh cho SRV-INTERNAL

Chọn `SRV-INTERNAL` -> Desktop -> IP Configuration:

```text
IP Address:      192.168.50.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.50.1
DNS Server:      192.168.50.10
```

### 10.4 Kiểm tra Layer 3

Trên core:

```text
show ip interface brief
show ip route
ping 192.168.50.10
ping 192.168.99.11
```

Để test trước khi DHCP được cấu hình, tạm gán IP tĩnh cho các PC:

```text
IT-PC-01:    192.168.10.10/24, gateway 192.168.10.1
HR-PC-01:    192.168.20.10/24, gateway 192.168.20.1
ACC-PC-01:   192.168.30.10/24, gateway 192.168.30.1
EMP-PC-01:   192.168.40.10/24, gateway 192.168.40.1
GUEST-PC-01: 192.168.60.10/24, gateway 192.168.60.1
```

Từ IT-PC-01 kiểm tra:

```text
ping 192.168.10.1
ping 192.168.20.10
ping 192.168.50.10
```

Giai đoạn này các VLAN phải liên lạc được tự do vì chưa áp dụng ACL. Nếu không ping được, không chuyển sang bước DHCP.

Lưu `v04-inter-vlan-routing.pkt` và commit:

```text
feat: enable inter-vlan routing on the core switch
```

## 11. Mốc 5 - DHCP, DNS và HTTP nội bộ

### 11.1 DHCP trên SRV-INTERNAL

Chọn `SRV-INTERNAL` -> Services -> DHCP -> On.

Tạo từng pool:

| Pool | Gateway | DNS | Start IP | Mask | Maximum users |
|---|---|---|---|---|---:|
| IT_POOL | 192.168.10.1 | 192.168.50.10 | 192.168.10.100 | 255.255.255.0 | 50 |
| HR_POOL | 192.168.20.1 | 192.168.50.10 | 192.168.20.100 | 255.255.255.0 | 50 |
| ACC_POOL | 192.168.30.1 | 192.168.50.10 | 192.168.30.100 | 255.255.255.0 | 50 |
| EMP_POOL | 192.168.40.1 | 192.168.50.10 | 192.168.40.100 | 255.255.255.0 | 50 |
| GUEST_POOL | 192.168.60.1 | 192.168.50.10 | 192.168.60.100 | 255.255.255.0 | 50 |

Không tạo DHCP pool cho VLAN 50 và 99 vì server và thiết bị quản trị dùng IP tĩnh.

### 11.2 Chuyển PC sang DHCP

Trên từng PC: Desktop -> IP Configuration -> DHCP.

Kiểm tra bằng Command Prompt:

```text
ipconfig /all
ping <default-gateway>
```

Nếu PC không nhận IP:

1. Kiểm tra access VLAN.
2. Kiểm tra trunk allowed VLAN.
3. Kiểm tra SVI up/up.
4. Kiểm tra `ip helper-address`.
5. Kiểm tra DHCP service đang On và pool đúng.

### 11.3 DNS và HTTP

Trên SRV-INTERNAL:

1. Services -> DNS -> On.
2. Thêm A record `intranet.company.local` -> `192.168.50.10`.
3. Services -> HTTP -> On.
4. Chỉnh trang index nếu muốn ghi tên project.

Từ PC:

```text
nslookup intranet.company.local
ping intranet.company.local
```

Mở Web Browser và truy cập:

```text
http://intranet.company.local
```

Lưu `v05-network-services.pkt` và commit:

```text
feat: configure centralized DHCP DNS and internal web services
```

## 12. Mốc 6 - Edge routing, ISP và NAT

### 12.1 Routed link trên core

```text
enable
configure terminal
interface gigabitEthernet0/1
 description L3_LINK_TO_R1_EDGE
 no switchport
 ip address 10.255.255.2 255.255.255.252
 no shutdown
exit
ip route 0.0.0.0 0.0.0.0 10.255.255.1
end
write memory
```

### 12.2 R1-EDGE

```text
enable
configure terminal
interface gigabitEthernet0/0
 description WAN_TO_ISP
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown
interface gigabitEthernet0/1
 description LAN_TO_CORE
 ip address 10.255.255.1 255.255.255.252
 ip nat inside
 no shutdown
exit
ip route 192.168.0.0 255.255.0.0 10.255.255.2
ip route 0.0.0.0 0.0.0.0 203.0.113.1
access-list 1 permit 192.168.0.0 0.0.255.255
ip nat inside source list 1 interface gigabitEthernet0/0 overload
end
write memory
```

### 12.3 R2-ISP

```text
enable
configure terminal
interface gigabitEthernet0/0
 description LINK_TO_CUSTOMER_EDGE
 ip address 203.0.113.1 255.255.255.252
 no shutdown
interface gigabitEthernet0/1
 description PUBLIC_SERVER_NETWORK
 ip address 198.51.100.1 255.255.255.0
 no shutdown
end
write memory
```

### 12.4 SRV-PUBLIC

Desktop -> IP Configuration:

```text
IP Address:      198.51.100.10
Subnet Mask:     255.255.255.0
Default Gateway: 198.51.100.1
DNS Server:      192.168.50.10
```

Bật HTTP trên SRV-PUBLIC. Trên SRV-INTERNAL DNS, thêm:

```text
public.example.test -> 198.51.100.10
```

### 12.5 Kiểm tra Internet giả lập

Trên core:

```text
ping 10.255.255.1
ping 203.0.113.1
ping 198.51.100.10
```

Từ PC:

```text
ping 198.51.100.10
```

Sau khi PC tạo traffic, trên R1-EDGE:

```text
show ip route
show ip nat translations
show ip nat statistics
```

Lưu `v06-edge-nat.pkt` và commit:

```text
feat: add ISP simulation edge routing and NAT overload
```

## 13. Mốc 7 - ACL bảo vệ các VLAN

Chỉ thực hiện khi tất cả kết nối ở mốc 6 hoạt động.

### 13.1 Chính sách Guest

Guest được dùng DNS nội bộ và ra public network nhưng không được truy cập các mạng private khác.

```text
configure terminal
ip access-list extended GUEST_IN
 permit udp any eq bootpc any eq bootps
 permit icmp 192.168.60.0 0.0.0.255 host 192.168.60.1 echo
 permit udp 192.168.60.0 0.0.0.255 host 192.168.50.10 eq domain
 permit tcp 192.168.60.0 0.0.0.255 host 192.168.50.10 eq domain
 deny ip 192.168.60.0 0.0.0.255 192.168.0.0 0.0.255.255
 permit ip 192.168.60.0 0.0.0.255 any
exit
interface vlan 60
 ip access-group GUEST_IN in
exit
end
```

### 13.2 Cách ly HR khỏi Accounting

```text
configure terminal
ip access-list extended HR_IN
 permit udp any eq bootpc any eq bootps
 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip 192.168.20.0 0.0.0.255 any
exit
interface vlan 20
 ip access-group HR_IN in
exit
```

### 13.3 Cách ly Accounting khỏi HR

```text
ip access-list extended ACCOUNTING_IN
 permit udp any eq bootpc any eq bootps
 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip 192.168.30.0 0.0.0.255 any
exit
interface vlan 30
 ip access-group ACCOUNTING_IN in
exit
end
write memory
```

### 13.4 Kiểm tra ACL

```text
show access-lists
show ip interface vlan 20
show ip interface vlan 30
show ip interface vlan 60
```

Test:

- Guest -> internal server: phải fail.
- Guest -> public server: phải thành công.
- HR -> Accounting PC: phải fail.
- HR -> internal server: phải thành công.
- Accounting -> HR PC: phải fail.
- Accounting -> public server: phải thành công.

Nếu một kết nối hợp lệ bị chặn, dùng `show access-lists` xem counter của dòng nào tăng. Không xóa toàn bộ ACL; xác định rule sai và sửa đúng dòng.

Lưu `v07-security-acl.pkt` và commit:

```text
security: enforce inter-vlan access control policies
```

## 14. Mốc 8 - SSH chỉ cho VLAN IT

SSH version 2 được bật trên các thiết bị doanh nghiệp. Một standard ACL chỉ cho phép nguồn từ VLAN IT truy cập các VTY line.

> Replace all placeholders with private lab-only credentials. Never commit real passwords or reusable credentials to a public repository.

### Cấu hình trên SW-CORE-L3 và R1-EDGE

```text
enable
configure terminal
ip domain-name company.local
username netadmin privilege 15 secret <LAB_SSH_SECRET>
crypto key generate rsa
```

Khi được hỏi modulus, nhập `1024` hoặc `2048` nếu model hỗ trợ.

```text
ip ssh version 2

ip access-list standard SSH_FROM_IT
 remark ALLOW_ONLY_IT_VLAN
 permit 192.168.10.0 0.0.0.255
 deny any
exit

line vty 0 4
 access-class SSH_FROM_IT in
 login local
 transport input ssh
 exec-timeout 10 0
exit

end
copy running-config startup-config
```

### Cấu hình trên SW-ACCESS-01, SW-ACCESS-02 và SW-ACCESS-03

Packet Tracer 2960 không lưu ổn định privilege level 15 được gắn trực tiếp vào local username. Vì vậy, access switch sử dụng hai lớp xác thực: SSH login và enable secret.

```text
enable
configure terminal
enable secret <LAB_ENABLE_SECRET>
ip domain-name company.local
username netadmin secret <LAB_SSH_SECRET>
crypto key generate rsa
```

Khi được hỏi modulus, nhập `1024` hoặc `2048` nếu model hỗ trợ.

```text
ip ssh version 2

ip access-list standard SSH_FROM_IT
 remark ALLOW_ONLY_IT_VLAN
 permit 192.168.10.0 0.0.0.255
 deny any
exit

line vty 0 15
 access-class SSH_FROM_IT in
 login local
 transport input ssh
 exec-timeout 10 0
exit

end
copy running-config startup-config
```

### Kiểm tra từ IT-PC-01

Kết nối đến Access Switch:

```text
ssh -l netadmin 192.168.99.11
```

Sau khi nhập `<LAB_SSH_SECRET>`, quản trị viên vào user EXEC mode:

```text
SW-ACCESS-01>
```

Kiểm tra:

```text
show privilege
```

Kết quả mong đợi:

```text
Current privilege level is 1
```

Nâng lên privileged EXEC bằng enable secret riêng:

```text
enable
show privilege
```

Kết quả mong đợi:

```text
SW-ACCESS-01#
Current privilege level is 15
```

SSH từ HR-PC-01 phải bị từ chối trước khi xác thực. Telnet phải bị đóng vì VTY chỉ cho phép SSH.

Kiểm tra trên thiết bị:

```text
show ip ssh
show access-lists SSH_FROM_IT
show users
show running-config | section line vty
```

Không đưa mật khẩu hoặc hash của mật khẩu vào repository public. Khi xuất running-config sang file `.txt`, thay secret và password hash bằng `<REDACTED>`.

Commit của milestone:

```text
security: restrict SSH management access to the IT VLAN
```

## 15. Mốc 9 - Port Security và cổng không sử dụng

Trên các cổng có PC:

```text
configure terminal
interface fastEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
 spanning-tree portfast
 spanning-tree bpduguard enable
exit
```

Áp dụng tương tự lên các access port đang sử dụng.

Đưa cổng không sử dụng vào VLAN 999 và shutdown. Ví dụ trên switch chỉ dùng Fa0/1-2 và Gi0/1:

```text
interface range fastEthernet0/3 - 24
 description UNUSED_DISABLED
 switchport mode access
 switchport access vlan 999
 shutdown
exit
interface gigabitEthernet0/2
 description UNUSED_DISABLED
 switchport mode access
 switchport access vlan 999
 shutdown
exit
end
write memory
```

Phải điều chỉnh range theo cổng thực tế; không shutdown uplink hoặc cổng PC đang dùng.

Kiểm tra:

```text
show port-security
show port-security interface fastEthernet0/1
show interfaces status
```

Commit:

```text
security: enable access port security and disable unused ports
```

## 16. Mốc 10 - Kiểm thử chính thức

Điền kết quả thật vào `docs/test-results.md`. Không ghi `Pass` nếu chưa tự chạy.

Các lệnh bằng chứng cần lưu:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show access-lists
show ip nat translations
show ip nat statistics
show ip ssh
show port-security
show running-config
```

Ảnh tối thiểu:

1. Toàn bộ topology.
2. VLAN và trunk.
3. Routing table.
4. Client nhận DHCP.
5. Truy cập intranet bằng DNS name.
6. NAT translation.
7. Guest bị chặn khỏi internal server.
8. Guest truy cập được public server.
9. IT SSH thành công.
10. HR SSH thất bại.

Tên ảnh nên mô tả nội dung, ví dụ `tc-07-guest-internal-denied.png`, không dùng `Screenshot1.png`.

Commit:

```text
test: document connectivity security and management verification
```

## 17. Mốc 11 - Xuất cấu hình

Trên từng thiết bị chạy:

```text
show running-config
```

Sao chép vào đúng file trong `configs/`. Thay tất cả secret/password bằng `<REDACTED>` trước khi commit.

Không cần xóa:

- Hostname.
- VLAN.
- IP private.
- Documentation IP như `203.0.113.0/24` và `198.51.100.0/24`.
- ACL và routing configuration.

Lưu file cuối cùng:

```text
packet-tracer/secure-enterprise-network-final.pkt
```

Commit:

```text
docs: add sanitized device configurations and final lab file
```

## 18. Mốc 12 - Hoàn thiện README

README phải phản ánh kết quả thật. Hoàn thành các phần:

- Overview.
- Business requirements.
- Architecture image.
- Device inventory.
- VLAN/IP plan.
- Implemented technologies.
- Security policies.
- Test results.
- Challenges and fixes.
- How to open the `.pkt` file.
- Limitations.
- Future improvements.

Không dùng câu chung chung như “The network is very secure”. Thay bằng bằng chứng cụ thể như “Guest VLAN traffic to RFC1918 internal networks is denied by an inbound extended ACL while DNS and public-server access remain available.”

Commit cuối:

```text
docs: finalize project README and implementation evidence
```

## 19. Cách xử lý lỗi theo lớp

Khi ping thất bại, kiểm tra theo thứ tự:

1. PC có đúng IP/mask/gateway không?
2. Link có màu xanh không?
3. Access port có đúng VLAN không?
4. VLAN có tồn tại trên cả hai switch không?
5. Trunk có mang VLAN đó không?
6. SVI có up/up không?
7. Core có route không?
8. ACL có chặn không?
9. Edge router có route trở về LAN không?
10. NAT inside/outside có đúng không?

Các lệnh quan trọng:

```text
show interfaces status
show vlan brief
show interfaces trunk
show mac address-table
show ip interface brief
show ip route
show access-lists
show ip nat translations
traceroute <destination>
```

## 20. Cách trình bày project khi phỏng vấn

Trình bày trong 3 phút:

1. Bài toán: mạng doanh nghiệp cần phân chia phòng ban và kiểm soát truy cập.
2. Thiết kế: core L3, access switches, edge router, internal services và ISP giả lập.
3. Triển khai: VLAN, trunk, SVI, DHCP relay, DNS, NAT, ACL, SSH, port security.
4. Kiểm thử: nói một test được phép và một test bị chặn.
5. Lỗi đã gặp: trình bày nguyên nhân, lệnh kiểm tra và cách sửa.
6. Giới hạn: đây là mô phỏng Packet Tracer, chưa phải production deployment.

Không học thuộc cấu hình. Hãy giải thích đường đi của packet và lý do chọn mỗi chính sách.
