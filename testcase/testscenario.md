

---

## 1. Danh sách Kịch bản Kiểm thử tổng quát (Test Scenarios)

# Bảng Kịch bản Kiểm thử Tổng quát (General Test Scenarios) - CAB System

**Dự án:** CAB System – Nền tảng đặt xe trực tuyến (Công ty ABC)[cite: 1]  
**Tài liệu tham chiếu:** SRS v1.0[cite: 1] & API Specifications  

| Scenario ID | Module / Service | FR ánh xạ | Test Scenario (Kịch bản kiểm thử) | Mục tiêu kiểm thử | Priority |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_AUTH** | Authentication Service | FR01, FR02[cite: 1] | Kiểm thử toàn diện chức năng Đăng ký và Đăng nhập hệ thống | Kiểm tra tính hợp lệ dữ liệu tài khoản, phân quyền vai trò (Customer/Driver/Staff), cấp phát JWT token và cơ chế khóa tài khoản[cite: 1]. | High |
| **TS_TRIP** | Trip & Matching Service | FR07 – FR15[cite: 1] | Kiểm thử quy trình Đặt xe, Tìm tài xế và Điều phối chuyến đi | Kiểm tra tính hợp lệ của điểm đón/trả, thuật toán matching tài xế, xử lý chuyển giao khi tài xế từ chối và cập nhật tuần tự trạng thái chuyến[cite: 1]. | High |
| **TS_PAY** | Payment Service | FR17 – FR20[cite: 1] | Kiểm thử Tính cước và Xử lý thanh toán chuyến đi | Kiểm tra công thức tính cước theo quãng đường/loại xe, hỗ trợ thanh toán tiền mặt và tích hợp cổng thanh toán bên thứ ba khi thành công/thất bại[cite: 1]. | High |
| **TS_RATE** | Rating Service | FR30[cite: 1] | Kiểm thử Đánh giá và Phản hồi chất lượng chuyến đi | Kiểm tra điều kiện chỉ được đánh giá khi chuyến hoàn tất, ràng buộc thang điểm sao (1–5 sao) và lưu vết nhận xét của khách hàng[cite: 1]. | Medium |
| **TS_ADMIN** | Admin & Operation Service | FR03, FR23 – FR27[cite: 1] | Kiểm thử Quản trị vận hành, Phân quyền và Xử lý sự cố | Kiểm tra bảo vệ API nhạy cảm theo vai trò nhân viên, giám sát chuyến đi thời gian thực và ghi nhận audit log khi can thiệp sự cố[cite: 1]. | High |
---

