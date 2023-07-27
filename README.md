# Packet-Sniffing


file: p-sniffing.py
- Chúng ta sẽ bắt đầu quá trình demo và chạy thử sản phẩm bằng việc sử dụng ngôn ngữ python, ở phần này chúng ta chỉ test tính năng bắt các gói tin, nhưng chưa được giải mã IP và ICMP.

File: scan.py

Line 1 - 7: chúng ta sẽ khai báo các thư viện cần thiết cho quá trình bắt gói tin.

Line 9 : 1 host: Biến lưu trữ địa chỉ IP của máy mà chương trình sẽ lắng nghe các gói tin.
Line 12-57: các lớp cấu trúc IP và ICMP 


IP: Lớp định nghĩa cấu trúc cho tiêu đề của gói tin IP. Nó sử dụng thư viện ctypesđể xác định các trường dữ liệu trong IP tiêu đề, chẳng hạn như ihl, version, tos, len,...

ICMP: Lớp định nghĩa cấu trúc cho header của gói tin ICMP (Internet Control Message Protocol). Cũng sử dụng ctypesđể xác định các trường dữ liệu trong tiêu đề ICMP.

Xác định mạng con và thông báo cho phép thử:
subnet = "10.215.12.33/24"
magic_message = "PYTHONRULES!" 
subnet: Chuỗi định nghĩa mạng con mà chương trình sẽ gửi gói tin UDP đến.
magic_message: Chuỗi thông điệp đặc biệt mà chương trình sẽ kiểm tra trong gói tin ICMP bị phản hồi.

Hàm udp_sender()gửi gói tin UDP:
def udp_sender(subnet, magic_message): 
    # ...
Hàm udp_sender()sử dụng để gửi các gói tin UDP tới các địa chỉ IP trong mạng con đã được xác định ( subnet).

Khởi tạo socket raw và lắng nghe các gói tin:
if os.name == "nt":
    socket_protocol = socket.IPPROTO_IP 
else:
    socket_protocol = socket.IPPROTO_ICMP
   
sniffer = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket_protocol)
sniffer.bind((host, 0))
sniffer.setsockopt(socket.IPPROTO_IP, socket.IP_HDRINCL, 1)

if os.name == "nt":
    sniffer.ioctl(socket.SIO_RCVALL, socket.RCVALL_ON)

Đoạn mã kiểm tra hệ điều hành đang chạy (Windows hoặc Linux/macOS) và chọn giao thức socket raw phù hợp (IP hoặc ICMP).
Chương trình tạo một socket thô và liên kết nó với địa chỉ IP của máy đã xác định ( host).
Chương trình cài đặt các tùy chọn cho socket raw để nhận các gói tin có IP tiêu đề.

Bắt đầu gửi gói tin và lắng nghe các gói tin:
t = threading.Thread(target=udp_sender, args=(subnet, magic_message)) 
t.start()

try:
    while True:
        raw_buffer = sniffer.recvfrom(65565)[0] 
        # ...
Chương trình bắt đầu gửi các gói trong UDP bằng cách tạo một luồng (luồng) mới để thực hiện chức năng udp_sender().
Chương trình tiếp tục lắng nghe các gói tin đến thông qua socket thô và phân tích thông tin trong các gói tin IP và ICMP như đã giải thích trước đó.