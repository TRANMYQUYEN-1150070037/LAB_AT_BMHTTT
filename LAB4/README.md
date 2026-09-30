

## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Mã số sinh viên (MSSV):** 1150070037
* **Lớp:** 11_TMĐT
* **Tên bài thực hành:** BÀI LAB 4: KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

---

## 2. Phiên bản và Thông số môi trường
* **Nền tảng ảo hóa:** VMware Workstation
* **Cấu hình mạng:** **Host-Only** (Dải mạng: `192.168.56.0/24`, Subnet Mask: `255.255.255.0`)
* **Máy quét (Attacker / Scanner):**
  * Hệ điều hành: **Kali Linux**
  * Công cụ rà quét: **Nmap 7.99**
* **Máy mục tiêu 1 (Target 1 - Linux):**
  * Hệ điều hành: **Metasploitable 2** 
* **Máy mục tiêu 2 (Target 2 - Windows):**
  * Hệ điều hành: **Microsoft Windows 11** 
  * Dịch vụ kiểm thử: Dịch vụ chia sẻ tệp tin **SMB (Server Message Block)** trên cổng `445/tcp` (`microsoft-ds`)
  * Cơ chế phòng thủ: **Windows Defender Firewall with Advanced Security**, quản trị qua PowerShell

---

## 3. Cách dựng môi trường
1. Cấu hình card mạng của cả 3 máy ảo (**Kali Linux**, **Metasploitable 2**, và **Windows 11**) kết nối chung vào cùng một switch mạng ảo chế độ **Host-Only** để đảm bảo tính cô lập và định tuyến nội bộ an toàn.
2. Khởi động máy ảo **Metasploitable 2**, đăng nhập và chạy `ifconfig` để kiểm tra địa chỉ IP.
3. Khởi động máy ảo **Windows 11**, mở Command Prompt kiểm tra xác nhận địa chỉ IPv4 bằng lệnh `ipconfig`.
4. Đảm bảo cổng dịch vụ kiểm thử (`445/tcp`) đang mở và cấu hình tường lửa Windows cho phép nhận lưu lượng kiểm thử từ máy quét.

---

## 4. Các tình huống đã thực hiện

### Phần A: Rà quét và xuất báo cáo đa định dạng (Mục tiêu Metasploitable 2 / Linux) PASS
1. **Rà quét dịch vụ:** Thực hiện rà quét phát hiện phiên bản dịch vụ (`-sV`) và kiểm tra các lỗ hổng trên hệ thống máy đích.
2. **Xuất báo cáo đa định dạng:**
   * Lưu kết quả quét văn bản thường (`-oN`): `ket_qua.txt`.
   * Lưu kết quả cấu trúc XML (`-oX`): `ket_qua.xml`.
   * Lưu kết quả phục vụ lọc grep (`-oG`): `smb.txt`.
3. **Chuyển đổi giao diện báo cáo HTML:** Sử dụng công cụ `xsltproc` để biên dịch tệp `ket_qua.xml` thành `bao_cao.html`.

### Phần B: Đánh giá trước và sau khi Hardening (Mục tiêu Windows 11) PASS
1. **Tình huống 1 (Before Hardening):** Từ Kali Linux, quét và lưu trạng thái mở ban đầu của cổng `445/tcp` vào tệp `before_win.txt`:

## 5. Lỗi gặp phải và cách khắc phục

* **Lỗi 1 — Kali Linux không thể Ping tới Windows 11 (100% packet loss):**
  * **Hiện tượng:** Lệnh `ping -c 4 192.168.56.130` từ Kali Linux nhận kết quả `100% packet loss` dù hai máy cùng thuộc phân vùng mạng Host-Only.
  * **Nguyên nhân:** Windows Defender Firewall trên Windows 11 kích hoạt cơ chế bảo vệ mặc định chặn toàn bộ các gói tin ICMPv4 Echo Request gửi vào. Điều này khiến Nmap ở chế độ quét mặc định nhận định máy đích ngừng hoạt động (*"Host seems down"*) và tự động dừng tiến trình rà quét.
  * **Cách khắc phục:**
    * *Giải pháp cấu hình:* Mở Command Prompt (Run as administrator) trên Windows 11 và thực thi lệnh cho phép gói tin Ping đi qua tường lửa:
      ```cmd
      netsh advfirewall firewall add rule name="Allow_Ping" protocol=icmpv4:8,any dir=in action=allow
      ```

* **Lỗi 2 — Dừng dịch vụ SMB không làm thay đổi trạng thái cổng khi quét lại:**
  * **Hiện tượng:** Sau khi thực thi lệnh dừng dịch vụ mạng `LanmanServer` trên Windows 11, bản quét đối chứng `after_win.txt` từ Kali Linux vẫn ghi nhận cổng `445/tcp` ở trạng thái `open`.
  * **Nguyên nhân:** Socket kết nối ở tầng kernel của trình điều khiển driver SMB chưa được giải phóng hoàn toàn ngay lập tức, hoặc các tiến trình hệ thống phụ thuộc đã tự động kích hoạt lại kết nối lắng nghe.
  * **Cách khắc phục:** Chuyển sang giải pháp phòng thủ bằng cách tạo quy tắc chặn Inbound trực tiếp trên tường lửa Windows Defender Firewall đối với cổng 445 (`New-NetFirewallRule` với `Action: Block`) qua PowerShell với quyền Administrator. Kết quả quét kiểm tra sau đó trên tệp `after_win1.txt` đã ghi nhận trạng thái chuyển đổi thành công sang `filtered`.
  
