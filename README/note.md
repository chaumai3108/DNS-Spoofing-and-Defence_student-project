### **NOTE**



/etc/setoolkit/: cấu hình có apache hoặc không

/etc/ettercap/: cấu hình dns spoofing

/etc/resolv.conf: namesever



\# (!!!!) on Kali linux bind9's service is called "named", not "bind9"

/etc/bind/: cấu hình bind9



/etc/dnsmasq.conf: cấu hình dnsmasq



###### **Windows**

\# Xem thông tin các bộ adapter

&#x09;ipconfig /all

\# Đổi DNS tĩnh

&#x09;netsh interface ip set dns "Wi-Fi 2" static 10.0.0.1





###### **DNS cache**

\# Xóa cache, đóng trình duyệt

&#x09;# Trên Windows:

&#x09;	ipconfig /flushdns

&#x09;# Trên Ubuntu

&#x09;	sudo resolvectl flush-caches



###### **dnsmasq \& dnschef - Định tuyến nội bộ**

\# Tắt DHCP của VMware trên VMnet 3

&#x09;Windows → VMware Virtual Network Editor (chạy as Admin)

&#x09;→ chọn VMnet 3

&#x09;→ bỏ tick "Use local DHCP service to distribute IP addresses"

&#x09;→ Apply



\# Cài đặt:

&#x09;apt install dnsmasq -y



\# /etc/dnsmasq.conf

interface=eth0

dhcp-range=192.168.40.100,192.168.40.200,12h

dhcp-option=3,192.168.40.1        # gateway = Windows host

dhcp-option=6,192.168.40.129      # DNS = Kali (đây là mấu chốt)

no-resolv

server=8.8.8.8



\# Chạy dnschef

&#x20;   dnschef --fakeip 192.168.40.129 \\

&#x20;       --fakedomains facebook.com,www.facebook.com \\

&#x20;       --nameservers 8.8.8.8 \\

&#x20;       --port 5353 \\	# Dòng này cần đối chiếu, so sánh với kịch bản 7

&#x20;       --interface 0.0.0.0

\# Khởi động

systemctl restart dnsmasq



\# Victim xin DHCP lại

&#x09;sudo dhclient -r ens36 \&\& sudo dhclient ens36

\# Verify

resolvectl status | grep "DNS Servers"

\# → 192.168.40.129  ✓



###### **Port \& Ufw, Services**

\# Kiểm tra trạng thái ufw

&#x09;sudo ufw status verbose

\# Bật ufw

&#x09;sudo ufw enable

\# Cấu hình ufw mặc định

&#x09;sudo ufw default deny incomingrt

&#x09;sudo ufw default allow outgoing

\# Mở port ufw

&#x09;sudo ufw allow 80/tcp



\# Kiểm tra trạng thái port cụ thể:

&#x09;sudo netstat -tuln | grep 80



\# Kích hoạt/Kết thúc dịch vụ

&#x09;sudo systemctl start/stop <services> \&\& sudo systemctl enable/disable <services>

&#x09;# apache2, named, dnsmasq



\# IP forwarding (trách rớt gói tin khi thực hiện MitM)

&#x09;echo 1 | sudo tee /proc/sys/net/ipv4/ip\_forward





###### **Iptables - Prerouting**

\# Mở port ibtables

&#x09;sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

\# Kiểm tra rule iptables

&#x09;sudo iptables -L -v -n



\# Xóa Prerouting

&#x09;iptables -t nat -F PREROUTING

\# Kiểm tra

&#x09;iptables -t nat -L PREROUTING -n -v

