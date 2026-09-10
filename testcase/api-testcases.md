

---

# Tài Liệu API Test Cases – CAB System (IUH)

* **Dự án:** CAB System – Nền tảng đặt xe trực tuyến (Công ty ABC)


* **Người thực hiện:** Lê Công Hậu


* **Môn học:** Lập trình hướng dịch vụ


* **Tài liệu tham chiếu:** `SRS.md` & các file đặc tả trong thư mục `API Specification/`



---

## 1. Authentication Service (FR01, FR02)

| Test Case ID | Tên kịch bản kiểm thử | API Endpoint & Method | Input Data (Payload / Params) | Kết quả mong đợi (Expected Output) | Mã lỗi / HTTP Status | AC liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| **TC_AUTH_01** | Đăng ký tài khoản khách hàng thành công | `POST /auth/register` | Body đầy đủ: `full_name`, `phone`, `email`, `password` hợp lệ

 | Tạo tài khoản thành công, trả về thông tin `customer_id` | `201 Created` | AC01

 |
| **TC_AUTH_02** | Đăng ký thất bại do thiếu trường bắt buộc | `POST /auth/register` | Body khuyết trường `password` hoặc `phone` | Báo lỗi thiếu dữ liệu đầu vào | `400 Bad Request` | AC01

 |
| **TC_AUTH_03** | Đăng ký thất bại do số điện thoại/email đã tồn tại | `POST /auth/register` | Body chứa `phone` đã được đăng ký trước đó | Báo lỗi tài khoản đã tồn tại | `409 Conflict` / `400` | AC01

 |
| **TC_AUTH_04** | Đăng nhập thành công với thông tin chính xác | `POST /auth/login` | `{"username": "0901234567", "password": "Password@123"}` | Trả về `access_token` (JWT Bearer) và `role` | `200 OK` | AC01, AC02

 |
| **TC_AUTH_05** | Đăng nhập thất bại do sai mật khẩu | `POST /auth/login` | `{"username": "0901234567", "password": "WrongPassword"}` | Báo sai thông tin đăng nhập, không cấp token | `401 Unauthorized` | AC02

 |

---

## 2. Driver & Vehicle Service (FR05, FR06, FR16)

| Test Case ID | Tên kịch bản kiểm thử | API Endpoint & Method | Input Data (Payload / Params) | Kết quả mong đợi (Expected Output) | Mã lỗi / HTTP Status | AC liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| **TC_DRV_01** | Bật trạng thái sẵn sàng đón khách | `PATCH /drivers/status` | Header: `Bearer Token`<br>

<br>Body: `{"status": "AVAILABLE"}` | Chuyển trạng thái sẵn sàng nhận đề xuất chuyến

 | `200 OK` | AC09, RULE02

 |
| **TC_DRV_02** | Chuyển sang ngừng hoạt động (Offline) | `PATCH /drivers/status` | Header: `Bearer Token`<br>

<br>Body: `{"status": "OFFLINE"}` | Tài xế bị loại khỏi danh sách đề xuất chuyến mới

 | `200 OK` | AC09

 |
| **TC_DRV_03** | Cập nhật toạ độ GPS thời gian thực | `POST /drivers/location` | Header: `Bearer Token`<br>

<br>Body: `{"lat": 10.8221, "lng": 106.6868}` | Lưu toạ độ mới nhất để phục vụ matching

 | `200 OK` | AC13, FR16

 |
| **TC_DRV_04** | Cập nhật trạng thái khi chưa đăng nhập | `PATCH /drivers/status` | Không truyền `Authorization Header` | Từ chối truy cập | `401 Unauthorized` | NFR05

 |

---

## 3. Trip & Matching Service (FR07 -> FR15)

| Test Case ID | Tên kịch bản kiểm thử | API Endpoint & Method | Input Data (Payload / Params) | Kết quả mong đợi (Expected Output) | Mã lỗi / HTTP Status | AC liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| **TC_TRIP_01** | Khách hàng đặt xe thành công | `POST /trips` | Header: `Bearer Token`<br>

<br>Body: điểm đón, điểm đến, `vehicle_type: "4_SEATS"`<br> | Tạo chuyến đi thành công, trạng thái ban đầu là `SEARCHING`<br> | `201 Created` | AC03

 |
| **TC_TRIP_02** | Đặt xe thất bại do thiếu điểm đón hoặc điểm đến | `POST /trips` | Header: `Bearer Token`<br>

<br>Body: `pickup_location: ""`<br> | Báo lỗi dữ liệu không hợp lệ, không tạo chuyến

 | `400 Bad Request` | AC04

 |
| **TC_TRIP_03** | Khách hàng theo dõi trạng thái chuyến đi | `GET /trips/{trip_id}` | Header: `Bearer Token`<br>

<br>Path: `trip_id` hợp lệ | Trả về thông tin trạng thái mới nhất của chuyến

 | `200 OK` | AC05

 |
| **TC_TRIP_04** | Theo dõi chuyến đi với mã ID không tồn tại | `GET /trips/{trip_id}` | Path: `trip_id = "invalid_id_999"` | Trả về thông báo không tìm thấy chuyến đi | `404 Not Found` | EXC02

 |
| **TC_TRIP_05** | Tài xế chấp nhận chuyến xe | `POST /trips/{trip_id}/respond` | Header: `Bearer Driver_Token`<br>

<br>Body: `{"action": "ACCEPT"}` | Chuyến xe gán cho tài xế, trạng thái đổi thành `DRIVER_ASSIGNED`<br> | `200 OK` | AC10, RULE01

 |
| **TC_TRIP_06** | Tài xế từ chối đề xuất chuyến | `POST /trips/{trip_id}/respond` | Header: `Bearer Driver_Token`<br>

<br>Body: `{"action": "REJECT"}` | Ghi nhận từ chối, kích hoạt tìm tài xế kế tiếp

 | `200 OK` | AC10, AC11, FR12

 |
| **TC_TRIP_07** | Tài xế cập nhật trạng thái chuyến theo trình tự | `PATCH /trips/{trip_id}/status` | Body: `{"status": "ARRIVED_AT_PICKUP"}`<br> | Trạng thái chuyến đi chuyển sang "Đã đến điểm đón"

 | `200 OK` | AC12, FR15

 |
| **TC_TRIP_08** | Cập nhật trạng thái sai quy trình | `PATCH /trips/{trip_id}/status` | Chuyến mới nhận nhưng cập nhật nhảy cóc sang `COMPLETED` | Từ chối cập nhật do vi phạm thứ tự quy trình

 | `400 Bad Request` | AC12

 |

---

## 4. Payment & Fare Service (FR17 -> FR20)

| Test Case ID | Tên kịch bản kiểm thử | API Endpoint & Method | Input Data (Payload / Params) | Kết quả mong đợi (Expected Output) | Mã lỗi / HTTP Status | AC liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| **TC_PAY_01** | Tính cước phí chuyến đi khi hoàn thành | `GET /trips/{trip_id}/fare` | Path: `trip_id` của chuyến đã hoàn thành

 | Trả về số tiền chính xác theo khoảng cách

 | `200 OK` | AC06, AC15

 |
| **TC_PAY_02** | Thanh toán bằng tiền mặt | `POST /payments/checkout` | Body: `{"trip_id": "...", "method": "CASH"}`<br> | Ghi nhận thanh toán thành công trực tiếp

 | `200 OK` | AC06, FR18

 |
| **TC_PAY_03** | Thanh toán trực tuyến qua cổng bên ngoài | `POST /payments/checkout` | Body: `{"trip_id": "...", "method": "ONLINE"}`<br> | Trả về đường dẫn thanh toán `payment_url`<br> | `200 OK` | AC07, FR19

 |
| **TC_PAY_04** | Xử lý khi thanh toán trực tuyến thất bại | `POST /payments/checkout` | Thử thanh toán khi cổng thanh toán báo lỗi

 | Báo lỗi thanh toán thất bại, cho phép thử lại

 | `400` / `502 Bad Gateway` | AC07, EXC03

 |

---

## 5. Rating Service (FR30)

| Test Case ID | Tên kịch bản kiểm thử | API Endpoint & Method | Input Data (Payload / Params) | Kết quả mong đợi (Expected Output) | Mã lỗi / HTTP Status | AC liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| **TC_RATE_01** | Khách hàng đánh giá tài xế hợp lệ | `POST /trips/{trip_id}/rating` | Header: `Bearer Token`<br>

<br>Body: `{"score": 5, "comment": "Tốt"}`<br> | Lưu đánh giá thành công gắn liền với chuyến đi

 | `201 Created` | AC08, FR30

 |
| **TC_RATE_02** | Đánh giá điểm số ngoài thang điểm (1-5) | `POST /trips/{trip_id}/rating` | Header: `Bearer Token`<br>

<br>Body: `{"score": 6}` | Báo lỗi giá trị thang điểm không hợp lệ | `400 Bad Request` | AC08

 |

---

## 6. Operation & Admin Service (FR03, FR23 -> FR28)

| Test Case ID | Tên kịch bản kiểm thử | API Endpoint & Method | Input Data (Payload / Params) | Kết quả mong đợi (Expected Output) | Mã lỗi / HTTP Status | AC liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| **TC_ADM_01** | Nhân viên vận hành tạo tài khoản tài xế | `POST /admin/drivers` | Header: `Bearer Admin_Token`<br>

<br>Body: thông tin tài xế mới

 | Tạo tài khoản tài xế thành công

 | `201 Created` | FR03, RULE04

 |
| **TC_ADM_02** | Khách hàng cố tình truy cập chức năng Admin | `POST /admin/drivers` | Header: `Bearer Customer_Token` | Từ chối truy cập do sai phân quyền

 | `403 Forbidden` | AC21, RULE04

 |
| **TC_ADM_03** | Giám sát các chuyến xe đang diễn ra | `GET /admin/trips/live` | Header: `Bearer Admin_Token` | Hiển thị đầy đủ danh sách chuyến thời gian thực

 | `200 OK` | AC18, FR23

 |
| **TC_ADM_04** | Can thiệp xử lý chuyến xe gặp sự cố | `POST /admin/trips/{trip_id}/resolve` | Header: `Bearer Admin_Token`<br>

<br>Body: `action: "CANCEL_TRIP"`<br> | Huỷ chuyến, lưu vết thao tác (Audit Log)

 | `200 OK` | AC19, RULE05

 |
| **TC_ADM_05** | Xuất báo cáo thống kê doanh thu vận hành | `GET /admin/reports/summary` | Query: `from_date=2026-09-01&to_date=2026-09-30`<br> | Trả về tổng chuyến, doanh thu, tỷ lệ huỷ

 | `200 OK` | AC20, FR28

 |

---
