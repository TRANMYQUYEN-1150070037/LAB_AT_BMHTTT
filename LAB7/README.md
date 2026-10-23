##  THÔNG TIN </br>
Họ và tên: Trần Mỹ Quyên </br>
Lớp: 11_TMĐT </br>
MSSV: 1150070037 </br>
Tên LAB: LAB 7 XÂY DỰNG CHÍNH SÁCH WEB PROTECTION TRÊN MÔ HÌNH pfSense + WEB PROXY </br>
</br>
##  KẾT QUẢ THỰC HÀNH </br>
### 1. Chuẩn bị hạ tầng và luồng dịch vụ Proxy
- **Cấu hình mạng WEB-PROXY:** Gán IP tĩnh `10.6.0.30/24` cho interface `ens33`, Gateway trỏ về pfSense `10.6.0.1`.
- **Kiểm tra luồng E2Guardian $\rightarrow$ Squid:** Cả hai tiến trình đều lắng nghe chuẩn xác:
  - `e2guardian`: `0.0.0.0:8080` (sẵn sàng tiếp nhận request từ Client A và Client B).
  - `squid`: `127.0.0.1:3128` (chỉ mở cho E2Guardian chuyển tiếp).
- **Cấu hình máy chủ DMZ-WEB:** Thiết lập Nginx Virtual Host và tạo 12 trang web mô phỏng phục vụ kiểm thử chính sách.
- **Tạo DNS nội bộ trên DC01:** Chạy tập lệnh PowerShell `Add-LAB7-DNS.ps1` tạo zone `lab7.test`. Lệnh `Resolve-DnsName` xác nhận các domain đều phân giải chính xác về `172.16.6.10`.
- **Đo kiểm thông tuyến:** Từ `WEB-PROXY`, vòng lặp kiểm tra tới 12 domain đều trả về trạng thái `[OK]`.

### 2. Cấu hình pfSense ép lưu lượng Web qua Proxy (Chống Bypass)
- **Tạo Aliases:**
  - `LAB7_CLIENTS`: Tập hợp IP của cả hai máy trạm kiểm thử `10.6.0.21` (Client A) và `10.6.0.22` (Client B).
  - `LAB7_PROXY`: Địa chỉ máy chủ Proxy `10.6.0.30`.
  - `WEB_PORTS`: Cổng web chuẩn `80, 443`.
  - `MAIL_PORTS`: Các cổng dịch vụ mail doanh nghiệp.
- **Ruleset trên Interface LAN:**
  1. Cho phép Anti-Lockout truy cập WebGUI pfSense.
  2. Cho phép Client truy vấn DNS về `DC01` (`10.6.0.10:53`).
  3. Cho phép `LAB7_CLIENTS` kết nối Proxy (`TCP/8080` tới `LAB7_PROXY`).
  4. Chặn tuyệt đối lưu lượng web trực tiếp từ `LAB7_CLIENTS` ra cổng `80, 443` (*Chan web truc tiep chong bypass*).
  5. Cho phép `LAB7_PROXY` đi ra ngoài Internet và truy cập Web DMZ (`172.16.6.10:80`).
  6. Explicit Deny ở cuối bảng.
- **Kết quả kiểm thử:**
  - *Bật proxy:* Client truy cập `http://normal.lab7.test` thành công qua proxy (HTTP 200 OK).
  - *Tắt proxy:* Cả Client A và Client B truy cập direct đều bị pfSense ngắt kết nối tại Lớp 4 (lỗi `ERR_CONNECTION_TIMED_OUT`).
  - *Từ WEB-PROXY:* Lệnh `curl -I http://normal.lab7.test` trả về mã `200 OK`, chứng minh chỉ máy chủ proxy mới có quyền định tuyến trực tiếp.

### 3. Xây dựng chính sách Web Protection
- **Mục E.1: Hard block (Chặn tuyệt đối không cho bypass)**
  - Đưa `jobs.lab7.test` và `china.lab7.test` vào tệp cấu hình cấm của E2Guardian.
  - Trên Client (bật proxy): Truy cập vào cả hai domain đều nhận trang khóa `Access Denied` với biểu tượng bàn tay đỏ cảnh báo, không xuất hiện liên kết mở tạm.
- **Mục E.4: Entertainment/Game theo thời gian (Time-Window Policy)**
  - Khởi tạo lịch trình `LAB7_ENT_60M` trên pfSense với khoảng thời gian ấn định 60 phút.
  - Thiết lập Firewall Rule LAN gắn Schedule `LAB7_ENT_60M` cho phép đi tới máy chủ DMZ `172.16.6.10`, kết hợp rule chặn ngoài giờ bên dưới.
  - Trên Client (tắt proxy): Trong khung giờ 60 phút của Schedule, truy cập vào `http://entertainment.lab7.test` và `http://172.16.6.10` thành công.
