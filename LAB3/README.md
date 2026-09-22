# Lab 3: Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Lớp:** 11_TMĐT
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

## 3. Thực hành dựng môi trường
* **Bước 1.** Tạo máy ảo Windows 11 Pro trên VMware Workstation Pro với cấu hình gồm 2 vCPU, 6 GB RAM, 64GB đĩa, cấu hình card mạng ở chế độ **Host-only**.
* **Bước 2.** Tạo cấu trúc thư mục làm việc tại `C:\LAB3` (bao gồm các thư mục con như `Evidence`, `Tools`, `Downloads`, `assets`).
* **Bước 3.** Giải nén gói dữ liệu bài lab `LAB3_Threats_Assets.zip` vào thư mục làm việc.
* **Bước 4.** Tiến hành cài đặt Python phiên bản `3.14.7` và Wireshark phiên bản `4.6.8` phục vụ công tác phân tích lưu lượng mạng và mã nguồn.
* **Bước 5.** Tải và triển khai bộ công cụ Sysinternals Suite (Sysmon, Autoruns, Process Explorer) từ trang chủ Microsoft để phục vụ giám sát tiến trình và hệ thống.

---

## 4. Các tình huống đã thực hiện và kết quả (PASS / FAIL)

| STT | Tình huống thực hành | Trạng thái kết quả |
| :---: | :--- | :---: |
| **TH1** | Xác định tài sản, lỗ hổng, mối đe dọa, rủi ro và phân loại đe dọa | **PASS** |
| **TH2** | Kiểm chứng chu trình phát hiện mã độc bằng EICAR | **PASS** |
| **TH3** | Tấn công mật khẩu, tạo tài khoản lab3user, cấu hình audit log và lọc sự kiện | **PASS** |
| **TH4 (Đang thực hiện)** | Cài đặt Sysmon, ghi nhận Process Create (Event ID 1) và thiết lập Persistence cơ bản | **Đang tiến hành** (Đã hoàn thành cài đặt Sysmon, kiểm tra Event ID 1 và chuẩn bị lệnh chạy Persistence). |

---
