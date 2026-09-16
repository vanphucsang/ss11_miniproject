# Mini Project – Hệ thống Quản lý Phòng khám Nha khoa **DentCare**

Sản phẩm phân tích – thiết kế hệ thống (SA/UML) cho phòng khám nha khoa DentCare.

## 1. Mục tiêu
Số hóa toàn bộ quy trình đặt lịch hẹn, khám bệnh, quản lý hồ sơ bệnh nhân và thanh toán viện phí của phòng khám DentCare (hiện đang vận hành hoàn toàn thủ công).

## 2. Cấu trúc repository

```
dentcare/
├── README.md                          # Tổng quan + bảng truy vết
├── docs/
│   ├── 01-phan-tich-he-thong.md       # Phần I  – Phân tích HTTT, SDLC, Stakeholder, User Story
│   ├── 02-activity-usecase.md         # Phần II – Activity Diagram & Use Case Diagram + đặc tả
│   ├── 03-class-diagram.md            # Phần III– Class Diagram
│   ├── 04-sequence-diagram.md         # Phần IV – Sequence Diagram
│   ├── 05-rang-buoc-phan-quyen.md     # Phần V  – Validation & Phân quyền
│   └── 06-hien-thi-bao-cao.md         # Phần VI – Hiển thị & Báo cáo
└── diagrams/puml/                     # Mã nguồn PlantUML của toàn bộ sơ đồ
    ├── activity-dat-lich-hen.puml
    ├── activity-kham-benh-thanh-toan.puml
    ├── usecase-tong-the.puml
    ├── class-benhnhan-lichhen.puml
    ├── class-thanh-toan.puml
    ├── class-tong-the.puml
    ├── sequence-dat-lich-hen.puml
    ├── sequence-kham-benh-tao-hoadon.puml
    └── sequence-huy-lich-hen.puml
```

> Sơ đồ được viết bằng **Mermaid** (hiển thị trực tiếp trên GitHub) và **PlantUML** (đúng ký pháp UML chuẩn, mở bằng https://www.plantuml.com/plantuml hoặc plugin PlantUML của VS Code / IntelliJ).

## 3. Phạm vi hệ thống

| Nhóm người dùng | Chức năng chính |
|---|---|
| Bệnh nhân | Đặt lịch hẹn, hủy lịch hẹn, xem hồ sơ cá nhân |
| Lễ tân | Quản lý lịch hẹn, đăng ký bệnh nhân, tạo hóa đơn, thanh toán |
| Nha sĩ | Khám bệnh, ghi hồ sơ điều trị, xem lịch làm việc |
| Quản lý | Quản lý danh mục dịch vụ, xem báo cáo thống kê doanh thu |

## 4. Bảng truy vết (Traceability Matrix)

Đảm bảo nhất quán: **User Story → Use Case → Class → Sequence**.

| ID | User Story | Use Case | Class liên quan | Sequence Diagram |
|---|---|---|---|---|
| US-01 | Bệnh nhân đặt lịch hẹn online | UC-01 Đặt lịch hẹn | Patient, Appointment, Dentist | SD-01 Đặt lịch hẹn |
| US-02 | Lễ tân đặt lịch hộ bệnh nhân | UC-01 Đặt lịch hẹn | Patient, Appointment, Dentist | SD-01 Đặt lịch hẹn |
| US-03 | Bệnh nhân hủy lịch hẹn | UC-02 Hủy lịch hẹn | Appointment | SD-03 Hủy lịch hẹn |
| US-04 | Lễ tân đăng ký hồ sơ bệnh nhân | UC-03 Quản lý hồ sơ bệnh nhân | Patient | — |
| US-05 | Nha sĩ ghi hồ sơ điều trị | UC-04 Khám bệnh & ghi hồ sơ | MedicalRecord, Appointment, DentalService | SD-02 Khám bệnh & Tạo hóa đơn |
| US-06 | Lễ tân tạo hóa đơn | UC-05 Tạo hóa đơn | Invoice, InvoiceDetail, DentalService | SD-02 Khám bệnh & Tạo hóa đơn |
| US-07 | Lễ tân thu tiền | UC-06 Thanh toán | Invoice, PaymentMethod, CashPayment, TransferPayment | SD-02 |
| US-08 | Quản lý quản trị danh mục dịch vụ | UC-07 Quản lý dịch vụ | DentalService | — |
| US-09 | Quản lý xem báo cáo doanh thu | UC-08 Báo cáo doanh thu | Invoice, InvoiceDetail | — |
| US-10 | Bệnh nhân/Lễ tân tra cứu hồ sơ | UC-09 Tra cứu hồ sơ bệnh nhân | Patient, MedicalRecord, Invoice | — |
| US-11 | Bệnh nhân nhận SMS nhắc hẹn | UC-10 Gửi SMS nhắc hẹn («extend») | Appointment | SD-01, SD-03 |

## 5. Cách xem sơ đồ PlantUML
1. Mở https://www.plantuml.com/plantuml/uml/
2. Dán nội dung file `.puml` trong `diagrams/puml/` → sơ đồ hiển thị ngay.

## 6. Thành viên & phân công
| Họ tên | Nhiệm vụ |
|---|---|
| (điền) | Phần I – Phân tích yêu cầu |
| (điền) | Phần II – Activity & Use Case |
| (điền) | Phần III – Class Diagram |
| (điền) | Phần IV – Sequence Diagram |
| (điền) | Phần V, VI – Ràng buộc, phân quyền, báo cáo |
