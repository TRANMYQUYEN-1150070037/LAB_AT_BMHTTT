
## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Mã số sinh viên (MSSV):** 115007--37
* **Tên bài thực hành:** BÀI LAB 4: KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

---

## 2. Phiên bản môi trường
* **Nền tảng ảo hóa:** VMware Workstation (Định danh MAC OUI: `00:0C:29`)[cite: 9, 13]
* **Cấu hình mạng:** Mạng Host-Only, dải địa chỉ IP `192.168.56.0/24`, Subnet Mask `255.255.255.0`[cite: 6, 7]
* **Máy quét (Scanner):** Kali Linux[cite: 9, 13]
* **Công cụ rà quét & xử lý:** Nmap phiên bản `7.99`[cite: 9, 13], công cụ chuyển đổi báo cáo XML sang HTML `xsltproc`
* **Máy mục tiêu (Target):** Microsoft Windows 11 (Phiên bản hệ điều hành: `10.0.26200.8037`), địa chỉ IP: `192.168.56.130`[cite: 7]
* **Dịch vụ kiểm thử:** Dịch vụ chia sẻ tệp SMB (Server Message Block) lắng nghe trên cổng `445/tcp` (`microsoft-ds`)[cite: 9, 13]
* **Cơ chế phòng thủ:** Windows Defender Firewall with Advanced Security, quản trị bằng Windows PowerShell

---

## 3. Cách dựng môi trường
1. Thiết lập hai máy ảo Kali Linux và Windows 11 kết nối chung vào cùng một switch mạng ảo chế độ **Host-Only**[cite: 6, 7].
2. Trên máy Windows 11, mở Command Prompt và chạy lệnh `ipconfig` để kiểm tra và xác nhận địa chỉ IPv4 là `192.168.56.130`[cite: 7].
3. Đảm bảo dịch vụ SMB và các quy tắc tường lửa cho phép chia sẻ tệp đang hoạt động bình thường trên Windows 11 để mở cổng `445/tcp` phục vụ kiểm thử.
4. Kiểm tra khả năng kết nối giữa Kali Linux và Windows 11 trong mạng nội bộ.

---

## 4. Các tình huống đã thực hiện
* **Tình huống 1 (Before Hardening):** Thực hiện rà quét phiên bản dịch vụ từ Kali Linux tới cổng 445 của Windows 11 bằng lệnh:
  ```bash
  sudo nmap -sV -p 445 192.168.56.130 -oN before_win.txt
