Dưới đây là hướng dẫn sử dụng chức năng của Hub IR (như Smart IR chạy firmware Openbeken phối hợp với Home Assistant qua MQTT)

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

Sử dụng đoạn mã YAML sau để cấu hình bộ kích hoạt (Trigger). Thay thế phần payload bằng đoạn mã bạn vừa copy ở Bước 1 và chỉnh lại entity_id của thiết bị bạn muốn điều khiển.

```yaml
alias: Tự động hóa mới
description: ""
triggers:
  - trigger: mqtt
    topic: Smart_IR1/#
    payload: "{\"IrReceived\":{\"Protocol\":\"Sony\",\"Bits\":12,\"Data\":\"0x89\"}}"
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


4. Bấm **Gửi đi (Lưu lại)** là hoàn thành. Bạn có thể đưa nút bấm này ra giao diện (Dashboard) để sử dụng.
