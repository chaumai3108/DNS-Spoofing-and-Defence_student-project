###### **APT Update**

\# Lỗi không thể update 

\# Mở Terminal và chạy lần lượt các lệnh sau:

&#x09;sudo wget https://archive.kali.org/archive-keyring.gpg -O /usr/share/keyrings/kali-archive-keyring.gpg

\# Sau đó cập nhật lại:

&#x09;sudo apt update



\# Nếu muốn kiểm tra an toàn hơn, sau khi chạy lệnh wget ở trên, kiểm tra hash của file key:

&#x09;sha1sum /usr/share/keyrings/kali-archive-keyring.gpg

###### 



###### **SE Toolkit**

(Trường hợp có sử dụng apache)

Nếu SET hiện thông báo:

&#x09;\[\*] You may need to copy /var/www/ into /var/www/html

\# Copy thủ công:

&#x09;sudo cp -r /var/www/\* /var/www/html/

&#x09;sudo chown -R www-data:www-data /var/www/html/





###### **Ettercap**

\# Trên Kali: kiểm tra lại file /etc/ettercap/etter.dns

\# Chạy Ettercap lại với lệnh có -d (debug).

&#x09;sudo ettercap -T -q -d -i eth0 -P dns\_spoof -M arp:remote /192.168.40.1// ///



\# Hoặc ettercap chỉ định

&#x09;sudo ettercap -T -q -d -i eth0 -P dns\_spoof -M arp:remote /10.15.0.254// /10.15.46.208// # IP Kali: 10.15.63.145



\# Trên Victim:

&#x09;nslookup facebook.com

\# Kiểm tra IP trả về.





###### **DNS Scope**

&#x20;# Sửa luồng dns ưu tiên

&#x09;sudo resolvectl default-route ens?? true/false

&#x09;ip route show

&#x09;resolvectl status

&#x09;sudo resolvectl reset-statist



\# Đặt DNS scope cho ens36

&#x09;sudo resolvectl dns ens36 192.168.40.1

&#x09;sudo resolvectl domain ens36 "\~."

&#x09;# "\~." = wildcard, mọi domain dùng link này



\# Bỏ DefaultRoute DNS khỏi ens33

&#x09;sudo resolvectl default-route ens33 false

&#x09;sudo resolvectl default-route ens36 true





###### **DNS routing**

\# Reset DNS cho cả 2 interface

&#x09;sudo resolvectl revert ens33

&#x09;sudo resolvectl revert ens36

\# Khôi phục default route ens33 (nếu đã xóa)

\# Kiểm tra route hiện tại

&#x09;ip route show



\# Nếu thiếu default route ens33 → thêm lại

&#x09;sudo ip route add default via 10.12.0.254 dev ens33 metric 102

\# Restart systemd-resolved

&#x09;sudo systemctl restart systemd-resolved

&#x09;sudo resolvectl status





###### **Namespace**

\# Kiểm tra DNS

&#x09;cat /etc/resolv.conf

\# Nếu trống hoặc sai → sửa:

&#x09;echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

\# Thử ping

&#x09;ping -c 3 8.8.8.8          # test kết nối IP

&#x09;ping -c 3 google.com       # test DNS

