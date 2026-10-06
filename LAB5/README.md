
## 1. Thông tin sinh viên
* **Họ và tên:** Trần Mỹ Quyên
* **Mã số sinh viên (MSSV):** 1150070037
* **Lớp:** 11_TMĐT
* **Tên bài thực hành:** BÀI LAB 5: THIẾT LẬP MÔ HÌNH TƯỜNG LỬA pfSense

---
## BÁO CÁO THỰC HÀNH: 

| STT | Hạng mục | Nội dung thực hiện | Trạng thái |
| :---: | :--- | :--- | :---: |
| **1** | **Cấu hình môi trường & pfSense** | • Cấu hình pfSense 3 card: WAN (DHCP), LAN (`10.0.0.1/8`), DMZ (`172.16.0.1/16`)<br>• Gán IP tĩnh cho DC (`10.0.0.2`) và Kali Linux | Hoàn thành (100%) |
| **2** | **Tình huống 1: Kiểm soát lưu lượng cơ bản** | • Pass dịch vụ Web (HTTP/HTTPS: port 80, 443)<br>• Pass dịch vụ DNS (port 53)<br>• Block gói tin kiểm tra ICMP (ping) ra ngoài | Hoàn thành (100%) |
| **3** | **Tình huống 2: Giới hạn Internet cho DC** | • Tắt các rule cũ, tạo rule Pass cho IP DC (`10.0.0.2`)<br>• Tạo rule Block toàn bộ dải mạng LAN (`LAN net`) ra Internet<br>• Reset States & đối chứng: DC ping ra ngoài thành công, LAN-Test (Kali) bị chặn hoàn toàn | Hoàn thành (100%) |
| **4** | **Tình huống 3: Cô lập DMZ khỏi LAN** | • **Bước A:** Mở tường lửa ICMP trên Windows Server<br>• **Bước B (Baseline):** Đặt IP DMZ cho Kali (`172.16.0.2`), tạo rule Pass DMZ -> Any, kiểm thử ping sang DC thành công<br>• **Bước C (Cô lập):** Đặt rule Block DMZ -> LAN lên trên rule Pass, Reset States & kiểm thử: DMZ ping DC thất bại, DMZ ping Internet thành công | Hoàn thành (100%) |
