
## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Mã số sinh viên (MSSV):** 1150070037
* **Lớp:** 11_TMĐT
* **Tên bài thực hành:** BÀI LAB 5: THIẾT LẬP MÔ HÌNH TƯỜNG LỬA pfSense

---
# BÁO CÁO THỰC HÀNH: 

## 1. Thông số thiết kế hệ thống (Network Topology)

* **pfSense Firewall 2.7.2:**
  * Interface WAN (`em0`): DHCP / NAT kết nối Internet.
  * Interface LAN (`em1`): `10.0.0.1/8` (Gắn kết nối VMware: `VMnet1 - Host-only`).
  * Interface DMZ (`em2`): `172.16.0.1/16` (Gắn kết nối VMware: `VMnet2 - Host-only`).
* **Domain Controller (Windows Server):**
  * Card mạng: `VMnet1`
  * IP Address: `10.0.0.2` | Subnet: `255.0.0.0` | Gateway: `10.0.0.1`
* **Kiểm thử viên (Kali Linux):**
  * Đóng vai trò linh hoạt: `LAN-Test` trong Tình huống 2 và `DMZ-Web` trong Tình huống 3.

---

## 2. Triển khai các tình huống kiểm soát lưu lượng

### Tình huống 1: Kiểm soát dịch vụ cơ bản từ vùng LAN
* **Mục tiêu:** Chỉ cho phép lưu lượng duyệt Web và phân giải tên miền, chặn ping ICMP.
* **Cấu hình trên pfSense (Tab LAN):**
  1. `Pass` | Protocol: `TCP` | Port: `80, 443` | Destination: `any`
  2. `Pass` | Protocol: `UDP/TCP` | Port: `53` | Destination: `any`
  3. `Block` | Protocol: `ICMP` | Source: `LAN subnets` | Destination: `any`
* **Kết quả:** Truy cập website và phân giải DNS thành công; ping ra ngoài bị chặn.

---

### Tình huống 2: Chỉ cấp quyền Internet duy nhất cho Domain Controller
* **Mục tiêu:** Cô lập toàn bộ các host trong LAN, chỉ cấp phép duy nhất cho máy DC (`10.0.0.2`) được ra Internet.
* **Cấu hình trên pfSense (Tab LAN):**
  * Tắt (Disable) toàn bộ rule mặc định và rule Tình huống 1.
  * Thứ tự rules thực thi:
    1. `Anti-Lockout Rule` (Mặc định).
    2. `Pass` | Protocol: `Any` | Source: `10.0.0.2` | Destination: `any` (`T2 - Pass DC 10.0.0.2`).
    3. `Block` | Protocol: `Any` | Source: `LAN subnets` | Destination: `any` (`T2 - Block LAN net`).
  * Thực hiện **Reset States** (`Diagnostics -> States -> Reset States`).
* **Kiểm thử đối chứng:**
  * **Domain Controller (`10.0.0.2`):** `ping 8.8.8.8` $\rightarrow$ Nhận phản hồi thành công (`0% loss`).
  * **Kali Linux (`10.0.0.3`):** `ping 8.8.8.8` $\rightarrow$ Bị chặn hoàn toàn (`100% packet loss`).

---

### Tình huống 3: Cô lập vùng DMZ khỏi mạng LAN nội bộ
* **Mục tiêu:** Cho phép DMZ ra Internet phục vụ dịch vụ, nhưng ngăn chặn hoàn toàn DMZ tấn công hoặc truy cập vào dải mạng LAN nội bộ.
* **Các bước triển khai:**
  1. **Bước A — Loại trừ Windows Firewall:**
     * Chạy trên CMD Domain Controller:
       ```cmd
       netsh advfirewall firewall add rule name="LAB-Allow-ICMPv4-Echo" protocol=icmpv4:8,any dir=in action=allow
       ```
  2. **Bước B — Kiểm thử Baseline (DMZ thông LAN trước khi chặn):**
     * Trên pfSense (Tab DMZ): Thêm rule `Pass` | Source: `DMZ subnets` | Destination: `any`.
     * Reset States.
     * Kiểm thử trên Kali (`172.16.0.2`): `ping -c 4 10.0.0.2` $\rightarrow$ Nhận phản hồi thành công (`0% loss`).
  3. **Bước C — Thiết lập luật cô lập:**
     * Trên pfSense (Tab DMZ), cấu hình danh sách rule theo thứ tự:
       1. `Block` | Protocol: `Any` | Source: `DMZ subnets` | Destination: `LAN subnets` (`Block DMZ to LAN`).
       2. `Pass` | Protocol: `Any` | Source: `DMZ subnets` | Destination: `any` (`Pass DMZ to Any`).
     * Reset States.
* **Kiểm thử nghiệm thu:**
  * Kali ping sang DC: `ping -c 4 10.0.0.2` $\rightarrow$ Bị chặn hoàn toàn (`100% packet loss`).
  * Kali ping ra Internet: `ping -c 4 8.8.8.8` $\rightarrow$ Thành công bình thường (`0% loss`).
  * Dọn dẹp rule tạm trên DC: `netsh advfirewall firewall delete rule name="LAB-Allow-ICMPv4-Echo"`.

---

## 3. Nhật ký xử lý sự cố (Troubleshooting Log)

1. **Mất IP cổng LAN trên pfSense sau khi thao tác bảng điều khiển:**
   * *Nguyên nhân:* Cổng `em1` bị gỡ địa chỉ mạng dẫn đến trình duyệt báo lỗi `ERR_CONNECTION_TIMED_OUT`.
   * *Khắc phục:* Truy cập Console pfSense, chọn Option `2) Set interface(s) IP address` $\rightarrow$ Gán lại IP `10.0.0.1/8`, tắt DHCP server và giữ nguyên cấu hình HTTPS.
2. **Lỗi `Error: Nexthop has invalid gateway` trên Kali Linux:**
   * *Nguyên nhân:* Nhập Default Gateway `172.16.0.1` trong khi card mạng vẫn đang giữ IP cũ `10.0.0.3/8` (lệch subnet mask).
   * *Khắc phục:* Chạy `sudo ip addr flush dev eth0` trước khi gán IP dải mới `172.16.0.2/16`.
3. **Lỗi `Destination Host Unreachable` (Lỗi ARP Layer 2):**
   * *Nguyên nhân:* Card mạng máy ảo Kali trên VMware chưa chọn đúng VMnet tương ứng với interface của pfSense.
   * *Khắc phục:* Đồng bộ card mạng Kali sang đúng `VMnet1` (ở Tình huống 2) và `VMnet2` (ở Tình huống 3), kiểm tra thông suốt bằng lệnh `ip neigh`.
