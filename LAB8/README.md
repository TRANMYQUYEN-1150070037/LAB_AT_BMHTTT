# Báo cáo Tiến độ Lab 9: DNS Spoofing & Cache Poisoning

## Thông tin sinh viên
- **Họ và tên:** Trần Mỹ Quyên
- **Mã sinh viên / Lớp:** 11_ĐH_TMDT / MSSV: ** 1150070037
- **Bài thực hành: **LAB 8 LOCAL DNS ATTACK DNS SPOOFING VÀ CACHE POISONING TRONG MẠNG ẢO CÔ LẬP
---

## Các bước đã hoàn thành 
## 1. Thiết lập Môi trường và Mô hình Mạng (Phần A, B, C)
* **Xây dựng mô hình mạng ảo:** Khởi tạo thành công các thành phần gồm pfSense (thêm interface `DNSLAB` IP `10.9.0.1/24`), DNS-Server (`10.9.0.10`), Victim (`10.9.0.100`), Attacker/Kali (`10.9.0.200`), App-Test (`172.16.6.20`) và DMZ-Web (`172.16.6.10`)
* **Cấu hình cách ly tường lửa pfSense:** Tạo các quy tắc (Rules) trên tab `DNSLAB` nhằm cô lập dải mạng lab, đồng thời mở rule cho phép DNS resolver truy vấn sang Authoritative DNS giả lập (`172.16.6.20`) và cho phép Victim truy cập web hợp lệ trong DMZ
## 2. Cấu hình DNS-Server và BIND9 (Phần D)
* **Cố định địa chỉ IP:** Thiết lập IP tĩnh `10.9.0.10/24` cho DNS-Server
* **Cài đặt và cấu hình BIND9:** 
  * Cài đặt gói `bind9` và `dnsutils` trên nền Ubuntu Server 24.04
  * Cấu hình chuyển tiếp (forward) phân giải tên miền `external.lab9.test` trỏ về máy **APP-TEST** (`172.16.6.20`)
  * Kiểm tra cú pháp zone và khởi động dịch vụ BIND9 thành công (`named -checkconf`, `systemctl start bind9`)

## 3. Cấu hình Victim và Khởi động Authoritative DNS (Phần E, F)
* **Cấu hình Victim:** Đảm bảo máy Victim (`10.9.0.100`) nhận phân giải DNS trực tiếp từ DNS-Server (`10.9.0.10`)
* **Khởi động Authoritative DNS Simulator trên APP-TEST:** Chạy thành công dịch vụ giả lập trên máy **App-Test** (`172.16.6.20`) thông qua script Python với độ trễ `1.5s`:
  ```bash
  sudo python3 auth_dns_delayed.py --bind 172.16.6.20 --delay 1.5
