
# PHẦN 1: Hướng Dẫn Sử Dụng Module 4 Relay & Học Lệnh RF 4 Kênh (ESPHome)

Tài liệu này hướng dẫn cách vận hành, học lệnh (learn) và xóa mã remote RF 433MHz trực tiếp bằng nút bấm vật lý trên thiết bị chạy mạch **CB3S (BK7231N)** cấu hình qua ESPHome.

## 📌 Các Chế Độ Hiển Thị Của Đèn LED (P22)

Đèn LED trạng thái trên thiết bị giúp bạn nhận biết nhanh tình trạng hoạt động:

* **Nháy chậm (0.5s tắt / 0.5s sáng):** Thiết bị đang mất kết nối với Home Assistant.
* **Nháy nhanh liên tục:** Thiết bị đang trong **Chế độ học lệnh (Learning Mode)**.
* **Sáng đứng 1 giây rồi tắt:** Đã học lệnh thành công.
* **Nháy nhanh 6 lần:** Đã xóa toàn bộ bộ nhớ mã RF thành công.
* **Tắt:** Thiết bị hoạt động bình thường, đã kết nối Home Assistant.

---

## 🛠 Quy Trình Vận Hành Thao Tác Nút Bấm

Sử dụng nút bấm **Learn Button (Chân P7)** để kích hoạt các chế độ học lệnh cho từng rơ-le (Relay) hoặc xóa trắng dữ liệu.

### 1. Cách Học Lệnh Remote Cho Từng Rơ-le

Khi thiết bị đang ở trạng thái bình thường (LED tắt), thực hiện thao tác nhấn nút bấm theo số lần quy định cho từng kênh:

| Kênh Cần Học | Thao Tác Kích Hoạt | Trạng Thái Đèn LED | Hành Động Tiếp Theo |
| --- | --- | --- | --- |
| **Relay 1** | **Nhấn và giữ** nút bấm trong **3 giây** | Đèn LED chuyển sang nháy nhanh | Nhấn 1 nút bất kỳ trên Remote RF cần học |
| **Relay 2** | Nhấn nhanh nút bấm **2 lần** | Đèn LED chuyển sang nháy nhanh | Nhấn 1 nút bất kỳ trên Remote RF cần học |
| **Relay 3** | Nhấn nhanh nút bấm **3 lần** | Đèn LED chuyển sang nháy nhanh | Nhấn 1 nút bất kỳ trên Remote RF cần học |
| **Relay 4** | Nhấn nhanh nút bấm **4 lần** | Đèn LED chuyển sang nháy nhanh | Nhấn 1 nút bất kỳ trên Remote RF cần học |

> 💡 **Lưu ý:** Ngay sau khi bạn bấm nút trên Remote, thiết bị nhận được mã sẽ tự động gán cho Relay mục tiêu, đèn LED sẽ **sáng đứng 1 giây** để báo hiệu thành công rồi tự thoát chế độ học. Dữ liệu mã RF được lưu vào bộ nhớ Flash (restore_value: yes) nên không bị mất khi mất điện.

### 2. Cách Xóa Toàn Bộ Mã Đã Học

Nếu muốn đổi Remote mới hoặc xóa toàn bộ các mã cũ đã lưu vào 4 rơ-le, hãy thao tác theo trình tự:

1. **Kích hoạt lệnh xóa bộ nhớ:** Yêu cầu thao tác dứt khoát.
Nhấn nút Learn (P7) liên tục 8 lần. Khoảng cách giữa các lần nhấn phải dưới 1 giây.


2. **Xác nhận trạng thái hệ thống:** Quan sát phản hồi từ đèn LED.
Hệ thống sẽ thực thi xóa sạch các biến relayX_code về giá trị 0. Đèn LED sẽ **nháy liên tục 6 lần** để báo hiệu bộ nhớ đã được định dạng lại hoàn toàn trống.


---

## 📡 Tích Hợp Hệ Thống & Chống Nhiễu

Mạch đã được cấu hình sẵn các bộ lọc phần mềm để tăng độ ổn định:

* **Chống lặp lệnh (Debounce):** Khóa nhận trùng một mã RF trong vòng **200ms** để tránh tình trạng rơ-le bị lật trạng thái liên tục khi bạn giữ nút remote quá lâu.
* **Thời gian trễ rơ-le (Interlock time):** Mỗi Relay có thời gian phản hồi giãn cách tối thiểu **700ms** giữa 2 lần bật/tắt để bảo vệ phụ tải (động cơ, đèn công suất lớn).
* **Đồng bộ Home Assistant:** Khi nhận tín hiệu RF, thiết bị tự động bắn một Sự kiện (Event) có tên esphome.rf_hub kèm dữ liệu code về Home Assistant để bạn tùy biến làm các tự động hóa (Automation) khác.

---

# PHẦN 2: Hướng Dẫn Cấu Hình Kết Nối Wifi, MQTT Cho Smart IR Chạy Firmware OpenBeken (OBK) và Đồng Bộ Tự Động Vào Home Assistant

Tài liệu này hướng dẫn chi tiết cách thiết lập một thiết bị mới sau khi đã nạp firmware **OpenBeken**, cấu hình kết nối MQTT Broker và kích hoạt tính năng Tự động nhận diện (**Home Assistant Discovery**).

---

## 1. Kết nối và Thiết lập Wifi cho Thiết bị

Khi thiết bị chạy OpenBeken khởi động lần đầu (hoặc sau khi reset), nó sẽ tự động phát một mạng Wifi độc lập (Access Point) để bạn cấu hình.

### Các bước thực hiện:

1. Sử dụng điện thoại hoặc máy tính, quét tìm mạng Wifi có tên dạng: OpenBK7231N_XXXXXX hoặc OpenBK7231T_XXXXXX.
2. Kết nối vào mạng Wifi này (Mạng không có mật khẩu).
3. Sau khi kết nối, trình duyệt sẽ tự động mở trang cấu hình. Nếu không tự mở, hãy truy cập địa chỉ IP mặc định: 192.168.4.1
4. Tại giao diện chính, chọn **Config** $\rightarrow$ Chọn **Configure WiFi**.
5. Nhập thông tin mạng Wifi nhà bạn:
* **SSID:** Tên Wifi (Bắt buộc phải là băng tần **2.4GHz**).
* **Password:** Mật khẩu Wifi.


6. Nhấn **Submit (Save)**. Thiết bị sẽ tự động khởi động lại và kết nối vào mạng Wifi nhà bạn.

> 💡 **Mẹo tìm IP thiết bị:** Sau khi thiết bị khởi động lại, bạn có thể vào cục Router Wifi để tìm IP mới cấp cho thiết bị, hoặc sử dụng các ứng dụng quét IP (như Fing trên điện thoại) để tìm IP của thiết bị OpenBeken trong mạng nội bộ.

---

## 2. Cấu hình Kết nối MQTT

Để OpenBeken có thể giao tiếp được với Home Assistant, thiết bị bắt buộc phải trỏ dữ liệu về địa chỉ của **MQTT Broker** (thông thường là Add-on Mosquitto Broker chạy trên HASS).

### Các bước thực hiện:

1. Gõ địa chỉ IP mới của thiết bị OpenBeken vào trình duyệt để truy cập lại trang quản trị.
2. Từ Menu chính, chọn **Config** $\rightarrow$ Chọn **Configure MQTT**.
3. Điền đầy đủ các thông số cấu hình MQTT Broker của bạn:

| Mục cấu hình | Giá trị mẫu | Giải thích |
| --- | --- | --- |
| **Host** | 192.168.1.X | Địa chỉ IP của máy chủ chạy Home Assistant (MQTT Broker) |
| **Port** | 1883 | Cổng MQTT mặc định |
| **User** | *Tên tài khoản* | Username cấu hình trong MQTT Broker của HASS |
| **Password** | *Mật khẩu* | Mật khẩu của tài khoản MQTT |
| **Client** | *Để mặc định* | Mã định danh duy nhất của thiết bị |
| **Topic** | obk_switch_1 | Tên Topic gốc đại diện cho thiết bị (Nên đặt viết liền không dấu) |

4. Nhấn **Submit** để lưu cấu hình. Thiết bị sẽ khởi động lại một lần nữa để thiết lập liên kết.
5. Sau khi thiết bị tải lại, hãy kiểm tra ở góc trên giao diện Web xem trạng thái MQTT đã báo **Connected** (Màu xanh) hay chưa.

---

## 3. Đồng bộ hóa Tự động vào Home Assistant (HA Discovery)

OpenBeken tích hợp sẵn giao thức tự động khai báo cấu hình linh hoạt (Home Assistant MQTT Discovery) tương tự như Tasmota, giúp thiết bị tự động xuất hiện trên HASS mà không cần viết code thủ công trong file configuration.yaml.

### Các bước thực hiện:

1. Tại giao diện Web của OpenBeken, chọn nút **Home Assistant** trên thanh menu (hoặc truy cập **Config** $\rightarrow$ **Start Home Assistant Discovery**).
2. Giao diện Discovery sẽ hiện ra, bạn nhấn vào nút: Start Discovery
3. Hệ thống sẽ ngay lập tức tạo và gửi các bản tin cấu hình (Discovery Payloads) qua giao thức MQTT tới Home Assistant để định hình các thực thể (Entity) như Công tắc (Switch), Đèn (Light), Cảm biến (Sensor) dựa trên cấu hình gán chân (Pin slot) trước đó của bạn.

---

## 4. Kiểm tra Thiết bị trên Home Assistant

1. Mở giao diện **Home Assistant**.
2. Truy cập vào **Cài đặt** (Settings) $\rightarrow$ **Thiết bị & Dịch vụ** (Devices & Services).
3. Tại dòng tích hợp **MQTT**, nhấn chọn mục **Thiết bị** (Devices).
4. Bạn sẽ nhìn thấy thiết bị OpenBeken mới xuất hiện với đầy đủ các nút bấm hoặc cảm biến tương ứng để bắt đầu điều khiển và viết Automation.

---

## ⚠️ Khắc phục Sự cố nhanh (Troubleshooting)

> 🛑 **CẢNH BÁO: Nhập sai thông tin Wifi khiến thiết bị mất kết nối**
> Nếu bạn vô tình điền sai tên hoặc mật khẩu Wifi làm thiết bị không thể bắt mạng, hãy tiến hành **Bật / Tắt nguồn cấp cho thiết bị liên tục 5 lần** (Chu kỳ bật khoảng 2-3 giây rồi tắt). Thiết bị sẽ nhận biết sự cố, tự động kích hoạt chế độ an toàn (**Safe Mode**) và phát lại mạng Wifi OpenBK7231X_XXXXXX để bạn cấu hình lại.

---

# PHẦN 3: Hướng Dẫn Sử Dụng Chức Năng Của Hub IR Phối Hợp Với Home Assistant Qua MQTT

---

## 1. Chức năng Hub IR làm cảm biến (Nghe tín hiệu remote để chạy Automation)

Dùng khi bạn muốn bấm một nút trên remote (TV, điều hòa, quạt...) để kích hoạt một kịch bản (Automation) hoặc bật/tắt thiết bị khác trên Home Assistant (HASS).

### Bước 1: Lấy thông điệp IR từ Remote

1. Vào **Cài đặt** $\rightarrow$ **Thiết bị & Dịch vụ** $\rightarrow$ Nhập MQTT vào ô tìm kiếm $\rightarrow$ Chọn **Cấu hình** (biểu tượng bánh răng).
2. Tại mục **Đăng ký vào chủ đề** (Listen to a topic), nhập: Smart_IR1/RESULT (hoặc Smart_IR2/RESULT).
3. Bấm **Bắt đầu nghe**.
4. Chĩa remote vào thiết bị Smart IR và nhấn nút muốn học.
5. **Copy toàn bộ** đoạn thông điệp (Payload) xuất hiện bên dưới.
* *Ví dụ mẫu:* {"IrReceived":{"Protocol":"Sony","Bits":12,"Data":"0x89"}}



### Bước 2: Tạo Automation trên Home Assistant

Sử dụng đoạn mã YAML sau để cấu hình bộ kích hoạt (Trigger). Thay thế phần payload bằng đoạn mã bạn vừa sao chép ở Bước 1 và chỉnh lại entity_id của thiết bị bạn muốn điều khiển.

```yaml
alias: Tự động hóa mới
description: ""
triggers:
  - trigger: mqtt
    topic: Smart_IR1/#
    payload: '{"IrReceived":{"Protocol":"Sony","Bits":12,"Data":"0x89"}}'
conditions: []
actions:
  - action: light.toggle
    metadata: {}
    target:
      entity_id: light.den_tv
    data: {}
mode: single

```

---

## 2. Chức năng Học lệnh và Phát lại (Tạo nút bấm điều khiển thiết bị)

Dùng khi bạn muốn tạo một nút bấm ảo trên giao diện HASS, khi nhấn vào nút đó, Hub IR sẽ phát ra tín hiệu để điều khiển TV, quạt, điều hòa...

### Bước 1: Học lệnh từ Remote

1. Vào **Cài đặt** $\rightarrow$ **Thiết bị & Dịch vụ** $\rightarrow$ Nhập MQTT vào ô tìm kiếm $\rightarrow$ Chọn **Cấu hình** (biểu tượng bánh răng).
2. Tại mục **Đăng ký vào chủ đề**, nhập: Smart_IR1/ir/get (hoặc Smart_IR2/ir/get).
3. Bấm **Bắt đầu nghe**.
4. Chĩa remote vào Smart IR và nhấn nút cần học lệnh.
5. **Copy chuỗi thông điệp** nhận được.
* *Ví dụ mẫu:* IR_Sony 0x1 0x65 0



### Bước 2: Tạo nút bấm (Template Button) trong HASS

1. Vào **Cài đặt** $\rightarrow$ **Thiết bị & Dịch vụ** $\rightarrow$ Chọn tab **Biến trợ giúp** (Helpers).
2. Bấm tạo mới, tìm từ khóa Template $\rightarrow$ Chọn **Nút bấm** (Button).
3. Thiết lập các thông số cho nút bấm:
* **Đặt tên nút:** (Ví dụ: Bật TV, Tắt Quạt...)
* **Hành động (Action):** Chọn mqtt: xuất bản (Mqtt: Publish).
* **Chủ đề (Topic):** Nhập cmnd/Smart_IR1/IRSEND (hoặc cmnd/Smart_IR2/IRSEND).
* **Phụ tải (Payload):** Tích chọn và dán chuỗi thông điệp đã học ở Bước 1, nhưng **bắt buộc loại bỏ chữ IR_ ở đầu**.
* *Ví dụ:* Chuỗi nhận được là IR_Sony 0x1 0x65 0 thì chỉ dán Sony 0x1 0x65 0.


* **QoS:** Chọn 1.


4. Bấm **Gửi đi (Lưu lại)** là hoàn thành. Bạn có thể đưa nút bấm này ra giao diện (Dashboard) ngoài màn hình để sử dụng.
