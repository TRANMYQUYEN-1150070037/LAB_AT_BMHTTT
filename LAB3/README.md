# Lab 3: Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Mã số sinh viên (MSSV):** 1150070037
* **Tên Lab:** Lab 3 - Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin
---

## 2. Phiên bản môi trường thực hành
| Thành phần | Phiên bản | Vai trò |
| :--- | :--- | :--- |
| **Ảo hóa** | VMware® Workstation Pro 26H1u1 | |
| **Máy ảo** | Windows 11 Pro, Version 25H2, OS Build 26200.8037 | Pro |
| **Sysmon** | 15.22 | |
| **Autoruns** | 14.3 | |
| **Process Explorer** | 17.14 | |
| **Python** | 3.14.7 | |
| **Wireshark** | 4.6.8 (v4.6.8-0-ge677bf052328) | |

---

## 3. Cách dựng môi trường
1. Tạo máy ảo Windows 11 trên VMware Workstation với cấu hình mạng **Host-only** và tạo snapshot sạch (`LAB3_CLEAN`).
2. Mở PowerShell với quyền Administrator, tạo cấu trúc thư mục làm việc tại `C:\LAB3` (bao gồm các thư mục `Evidence`, `Tools`, `Downloads`, `assets`)[cite: 1].
3. Giải nén và chuẩn bị bộ công cụ mã nguồn phục vụ bài lab[cite: 1].

---

## 4. Các tình huống đã thực hiện và kết quả (PASS / FAIL)

| STT | Tình huống thực hành | Trạng thái kết quả | Bằng chứng / Tệp lưu trữ |
| :---: | :--- | :---: | :--- |
| **TH1** | Xác định tài sản, lỗ hổng, mối đe dọa, rủi ro và phân loại đe dọa | **PASS**[cite: 1] | Hoàn thành bảng Risk Register và phân loại 5 nhóm nguồn đe dọa trong báo cáo[cite: 1]. |
| **TH2** | Kiểm chứng chu trình phát hiện mã độc bằng EICAR | **PASS**[cite: 1] | `defender_eicar.txt`, `H4_ProtectionHistory_EICAR.png`[cite: 1]. |
| **TH3** | Tấn công mật khẩu, tạo tài khoản lab3user, cấu hình audit log và lọc sự kiện | **PASS**[cite: 1] | `auth_events_before_rotation.txt`, `H5_Event4625.png`[cite: 1]. |
| **TH4 (Đang thực hiện)** | Cài đặt Sysmon, ghi nhận Process Create (Event ID 1) và thiết lập Persistence cơ bản | **Đang tiến hành** (Đã xong phần cấu hình Sysmon, kiểm tra Event ID 1 và chuẩn bị lệnh tạo Persistence)[cite: 1]. | `H6_Sysmon_Event1.png`[cite: 1]. |
