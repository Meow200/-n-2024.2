# BÁO CÁO DỰ ÁN

## **1. Giới Thiệu**
### **1.1. Tên dự án**
HỆ THỐNG KIỂM TRA NĂNG SUẤT CÔNG NHÂN BẰNG RFID VÀ ESP32

### **1.2. Mô tả tóm tắt**
Dự án nhằm xây dựng một hệ thống giám sát năng suất công nhân trên dây chuyền sản xuất bằng cách gán thẻ RFID cho sản phẩm, sử dụng ESP32 để ghi nhận thời gian vào/ra của sản phẩm tại hai đầu dây chuyền. Dữ liệu được ghi lại và gửi lên MySQL để phân tích năng suất công nhân theo ca.

---
## **2. Mục Tiêu Dự Án**
1. Ghi nhận thời gian sản phẩm vào/ra dây chuyền.
2. Tính toán thời gian sản xuất từ khi sản phẩm bắt đầu đến khi hoàn thành.
3. Lưu trữ dữ liệu lên MySQL Server.
4. Hiển thị thông tin năng suất theo ngày, ca làm việc.

---
## **3. Cơ Sở Lý Thuyết**
### **3.1. RFID (Radio Frequency Identification)**
- RFID là công nghệ sử dụng sóng vô tuyến để truyền dữ liệu.
- Mỗi thẻ RFID có mã UID duy nhất, được dùng để nhận diện sản phẩm.

### **3.2. ESP32**
- ESP32 là vi điều khiển có WiFi, Bluetooth, vận hành tốt với RFID.
- Giao tiếp với module RFID qua SPI.

### **3.3. MySQL**
- MySQL là hệ quản trị cơ sở dữ liệu quan hệ (SQL).
- Lưu dữ liệu vào bảng **production** gồm: `id`, `product_id`, `entry_time`, `exit_time`, `duration`. (chưa xong)

### **3.4. Giao Tiếp SPI**
SPI (Serial Peripheral Interface) là giao thức giao tiếp nối tiếp tốc độ cao giữa ESP32 và module RFID. SPI hoạt động theo nguyên tắc master-slave với các chân tín hiệu chính:
- **SCK (Serial Clock)**: Xung clock đồng bộ.
- **MOSI (Master Out, Slave In)**: Dữ liệu từ ESP32 gửi đến RFID.
- **MISO (Master In, Slave Out)**: Dữ liệu từ RFID gửi về ESP32.
- **SS (Slave Select)**: Chọn thiết bị giao tiếp.

ESP32 hoạt động ở chế độ master và điều khiển 2 module RFID thông qua các chân SS riêng biệt.

---
## **4. Thiết Kế Hệ Thống**
### **4.1. Sơ Đồ Kết Nối**
ESP32 giao tiếp với 2 module RFID RC522 qua SPI:



### **4.2. Luồng Hoạt Động**
1. RFID 1 quét thẻ → Lưu ID sản phẩm + thời gian vào.
2. RFID 2 quét thẻ → Lưu thời gian ra.
3. Tính **thời gian sản xuất** = `exit_time - entry_time`.
4. Gửi dữ liệu lên MySQL.

---
## **5. Phương Án Cũ: Firebase**
Trước khi chuyển sang MySQL, hệ thống sử dụng **Firebase Realtime Database** để lưu trữ dữ liệu:
1. ESP32 đọc thẻ RFID và lưu thông tin vào Firebase.
2. Firebase cập nhật dữ liệu theo thời gian thực.
3. Một ứng dụng web truy vấn Firebase để hiển thị năng suất.

### **Hạn Chế của Firebase**
- Firebase phù hợp với dữ liệu thời gian thực nhưng thiếu công cụ phân tích dữ liệu phức tạp.
- Không tối ưu cho truy vấn số lượng lớn.
- Phụ thuộc vào internet, khó quản lý offline.

Do đó, nhóm quyết định chuyển sang **MySQL** để tận dụng khả năng truy vấn mạnh mẽ và dễ mở rộng.

---
## **6. Cài Đặt & Lắp Ráp**
1. Kết nối phần cứng theo sơ đồ.
2. Làm một cơ sở dữ liệu MySQL.
3. Viết code ESP32 để:
   - Kết nối WiFi.
   - Đọc thẻ RFID.
   - Lưu dữ liệu vào MySQL.

---
## **7. Kết Quả & Đánh Giá**
### **7.1. Kết Quả**
- Hệ thống đo được thời gian sản xuất của từng sản phẩm.
- Gửi dữ liệu vào MySQL thành công.
- Truy vấn vào bảng MySQL để phân tích dữ liệu.

### **7.2. Hạn Chế & Hướng Phát Triển**
- **Hạn chế**: Có độ trễ sai nếu RFID không đọc được thẻ.
- **Phát triển**:
  - Kết hợp màn OLED hiển thị năng suất theo giờ.
  - Thêm cảm biến hỗ trợ kiểm tra lỗi sản phẩm.
  - Xây dựng dashboard web hiển thị dữ liệu trực quan hơn.
  - thêm quét qr và mã vạch

