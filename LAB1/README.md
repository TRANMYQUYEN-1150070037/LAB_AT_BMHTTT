Họ và tên: Trần Mỹ Quyên <br>
Mssv: 1150070037 <br>
Bài lab 1.1 Cài đặt và cấu hình môi trường <br>
Bài lab 1.2 BẮT GÓI TIN Telnet – SSH <br>
<br>
Nội dung đã thực hiện LAB 1.2<br>
1/ Thiết lập mô hình 3 máy ảo chung mạng LAN Segment: Kali Server (10.0.0.1/24), Windows 10 Client (10.0.0.2/24) và Windows 10 Attacker (10.0.0.3/24). <br>
2/ Cài đặt gói openssh-server và inetutils-telnetd trên Kali Linux.<br>
3/ Xử lý lỗi hệ thống trên Kali: mở khóa inetd (systemctl unmask inetd), khai báo quyền root và binary telnetd trong /etc/inetd.conf để kích hoạt cổng 23.<br>
4/ Khởi tạo tài khoản kiểm thử tênSV với mật khẩu MSSV trên Kali Server.<br>
5/ Cài đặt PuTTY trên Windows Client và Wireshark (kèm Npcap) trên Windows Attacker.<br>
6/ Tắt tường lửa Windows, thông mạng ICMP (ping) 2 chiều ổn định giữa cả 3 máy ảo.<br>
7/ Thực hiện kết nối Telnet từ Windows Client sang Kali Server và cấu hình theo dõi lưu lượng trên Windows Attacker.<br>
<br>
Kêt quả thực hiện <br>
1/ Mạng nội bộ 3 máy ảo hoạt động thông suốt (ping phản hồi < 1ms, 0% packet loss). <br>
2/ Hai cổng quản trị 22 (SSH) và 23 (Telnet) đều ở trạng thái LISTEN trên Server. <br>
3/ Windows Client đăng nhập Telnet thành công vào Kali Server.<br>
4/ Chưa thu thập thành luồng TCP Stream trên Wireshark

