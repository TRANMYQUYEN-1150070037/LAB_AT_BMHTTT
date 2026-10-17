## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Mã số sinh viên :** 1150070037
* **Lớp:** 11_TMĐT
* **Tên bài thực hành:** LAB6: XÁC THỰC ACTIVE DIRECTORY VÀ CHÍNH SÁCH TRUY CẬP TRÊN pfSense


## BÁO CÁO THỰC HÀNH: 
## 1. Hạ tầng & Phân vùng mạng (Network Topology)

Hệ thống được ảo hóa trên VMware Workstation, phân tách thành 4 phân vùng bảo mật:

* **WAN:** Cổng giao tiếp ngoại mạng (VMnet NAT / DHCP).
* **LAN (`10.6.0.1/24`):** Mạng nội bộ cho máy trạm và máy chủ xác thực.
* **DMZ (`172.16.6.1/24`):** Vùng dịch vụ cách ly chứa máy chủ web nội bộ.
* **MGMT (`192.168.6.1/24`):** Phân vùng quản trị thiết bị độc lập (Out-of-band).

### Các thiết bị và địa chỉ IP cấu hình:
* **pfSense Gateway:** `10.6.0.1` (LAN), `172.16.6.1` (DMZ).
* **DC01 (Windows Server):** `10.6.0.10` — Active Directory Domain Controller, DNS, LDAP (Port 389).
* **Client-A (Windows 11):** `10.6.0.21` — Máy trạm kiểm thử chính sách.
* **Client-B (Windows 10):** `10.6.0.22` — Máy trạm kiểm thử ngoại lệ.
* **Ubuntu Server (Web DMZ):**
  * `172.16.6.10`: `GENERAL_SITE` (Web nội bộ thông thường)
  * `172.16.6.20`: `JOB_SITE` (Web tuyển dụng)
  * `172.16.6.30`: `RESTRICTED_SITE` (Trang web độc hại/bị cấm)
  * `172.16.6.40`: `SOCIAL_SITE` (Mạng xã hội)

---

## 2. Các hạng mục đã hoàn thành

### 2.1. Cấu hình dịch vụ nền tảng
- [x] **Xác thực LDAP tập trung:** Kết nối pfSense với DC01 qua cổng 389, cấu hình Search Scope `Entire Subtree`, đồng bộ thành công tài khoản người dùng `sv_a`, `sv_b`.
- [x] **Captive Portal:** Kích hoạt trên cổng LAN (chuyển hướng qua port 8002), kiểm soát phiên duyệt web của người dùng qua tài khoản Active Directory.
- [x] **Khởi tạo danh mục Aliases:**
  * **IP / Host:** `POLICY_CLIENTS` (`10.6.0.21, 10.6.0.22`), `CLIENT_B` (`10.6.0.22`), `JOB_SITE` (`.20`), `RESTRICTED_SITE` (`.30`), `SOCIAL_SITE` (`.40`), `GENERAL_SITE` (`.10`).
  * **Port:** `WEB_PORTS` (80, 443).
- [x] **Lịch biểu (Schedules):** Thiết lập `LUNCH_WINDOW` (giờ nghỉ trưa), `WORK_HOURS` (giờ hành chính) và `TEST_NOW` (phục vụ nghiệm thu trực tiếp).

---

### 2.2. Triển khai & Nghiệm thu 5 kịch bản Firewall Rules

| Kịch bản | Interface | Tóm tắt cấu hình | Kết quả kiểm thử thực tế |
| :--- | :---: | :--- | :--- |
| **Kịch bản 1: Chặn JOB_SITE** | **LAN** | `Block POLICY_CLIENTS -> JOB_SITE : WEB_PORTS` | Client-A bị timeout khi vào `172.16.6.20`; vẫn truy cập được `GENERAL_SITE` (`172.16.6.10`); log Firewall ghi nhận bản ghi Block. |
| **Kịch bản 2: Cấm RESTRICTED_SITE** | **LAN** | `Block POLICY_CLIENTS -> RESTRICTED_SITE : WEB_PORTS` | Cả Client-A và Client-B đều bị chặn truy cập `172.16.6.30`; log Firewall ghi nhận bản ghi Block cấp nhóm. |
| **Kịch bản 3: Giới hạn SOCIAL_SITE** | **LAN** | 1. `Pass CLIENT_B -> SOCIAL_SITE : WEB_PORTS [Schedule]`<br>2. `Block POLICY_CLIENTS -> SOCIAL_SITE : WEB_PORTS [Fallback]` | Client-B mở được `172.16.6.40` khi lịch Active; ngoài lịch hoặc từ Client-A bị rule Fallback chặn đứng. |
| **Kịch bản 4: Giới hạn Web giờ hành chính** | **LAN** | 1. `Pass POLICY_CLIENTS -> Any : WEB_PORTS [Schedule]`<br>2. `Block POLICY_CLIENTS -> Any : Any [Fallback]` | Duyệt web thành công trong giờ; ping ICMP bị chặn hoàn toàn; ngoài giờ làm việc bị khóa toàn bộ lưu lượng. |
| **Kịch bản 5: Cô lập vùng DMZ** | **DMZ** | `Block DMZ net -> LAN net` | Ubuntu DMZ ping `10.6.0.10` bị mất gói 100% (bị drop tại tab DMZ); chiều ngược lại từ LAN sang DMZ web vẫn thông suốt nhờ Stateful Inspection. |

---
