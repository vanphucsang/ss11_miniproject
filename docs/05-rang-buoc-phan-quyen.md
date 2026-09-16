# Phần V – Ràng buộc nghiệp vụ và Phân quyền

## 5.1. Validation – Xác thực dữ liệu đầu vào

| Mã | Trường dữ liệu | Quy tắc | Biểu thức kiểm tra | Thông báo lỗi |
|---|---|---|---|---|
| V-01 | `Patient.phoneNumber` | Đúng 10 chữ số, bắt đầu bằng "0" | `^0\d{9}$` | "Số điện thoại phải gồm 10 chữ số và bắt đầu bằng 0" |
| V-02 | `Patient.dateOfBirth` | Không lớn hơn ngày hiện tại | `dateOfBirth <= today` | "Ngày sinh không được lớn hơn ngày hiện tại" |
| V-03 | `Appointment.appointmentDate` | Không nhỏ hơn ngày hiện tại | `appointmentDate >= today` | "Ngày hẹn không được nhỏ hơn ngày hiện tại" |
| V-04 | `DentalService.unitPrice` | Lớn hơn 0 | `unitPrice > 0` | "Đơn giá dịch vụ phải lớn hơn 0" |
| V-05 | `InvoiceDetail.quantity` | Lớn hơn hoặc bằng 1 | `quantity >= 1` | "Số lượng dịch vụ phải từ 1 trở lên" |
| V-06 | `Patient.fullName` | Bắt buộc, 2–100 ký tự | `NOT NULL, length 2..100` | "Vui lòng nhập họ tên bệnh nhân" |
| V-07 | Các mã định danh | Duy nhất toàn hệ thống | `UNIQUE` | "Mã đã tồn tại trong hệ thống" |

> **Nguyên tắc:** kiểm tra **2 tầng** – phía client (phản hồi tức thì, trải nghiệm tốt) và phía server/CSDL (ràng buộc CHECK, UNIQUE – bảo đảm toàn vẹn thật sự).

---

## 5.2. Ràng buộc nghiệp vụ (Business Rules)

| Mã | Ràng buộc | Nơi thể hiện |
|---|---|---|
| **BR-01** | Mỗi `timeSlot` của một Nha sĩ trong một ngày chỉ có **tối đa 1** lịch hẹn ở trạng thái "Đã xác nhận" – không được trùng lịch | Activity Diagram (Decision *Khung giờ còn trống?*), UC-01 bước 4 & 6, `Appointment.checkConflict()`, SD-01 khung `alt`, UNIQUE(dentistId, date, timeSlot) khi status='Đã xác nhận' |
| **BR-02** | Chỉ được hủy lịch khi trạng thái là "Chờ xác nhận" hoặc "Đã xác nhận"; trạng thái "Đã khám" **không thể** hủy | `Appointment.isCancellable()`, SD-03 khung `alt` |
| **BR-03** | Hóa đơn chỉ được tạo **sau khi** Nha sĩ hoàn tất ghi nhận `MedicalRecord` | Activity Diagram Phần II (Decision *Hồ sơ đã hoàn tất?*), SD-02 (gọi `taoHoaDon()` sau `hoanTatHoSo()`) |
| **BR-04** | `Invoice.totalAmount` = Σ `InvoiceDetail.lineTotal`, tính **tự động** | `Invoice.calculateTotal()`, Self Message `tinhTongTien()` trong SD-02 |
| **BR-05** | `InvoiceDetail.lineTotal` = `unitPrice × quantity` | `InvoiceDetail.calculateLineTotal()` |
| **BR-06** | Các mã `patientId`, `dentistId`, `appointmentId`, `recordId`, `invoiceId`, `serviceId` duy nhất toàn hệ thống | Class Diagram (thuộc tính khóa), PRIMARY KEY/UNIQUE |
| **BR-07** | Một `Appointment` thuộc **đúng 1** `Patient` và **đúng 1** `Dentist`; một `MedicalRecord` gắn **đúng 1** `Appointment`; một `Invoice` gắn **đúng 1** `MedicalRecord` | Multiplicity trong Class Diagram |
| **BR-08** | Xóa `Patient` **không** được xóa `MedicalRecord` và `Invoice` đã tạo (bảo toàn dữ liệu lịch sử) | Xóa mềm `Patient.isDeleted`; khóa ngoại `ON DELETE RESTRICT`; không dùng Composition giữa Patient và các thực thể này |
| **BR-09** | Trạng thái lịch hẹn chuyển theo đúng vòng đời: Chờ xác nhận → Đã xác nhận → Đã khám; hoặc → Đã hủy | State machine trong `AppointmentService` |
| **BR-10** | Hóa đơn "Đã thanh toán" không được sửa chi tiết | `InvoiceService` kiểm tra `status` trước khi cho phép cập nhật |

---

## 5.3. Ma trận phân quyền (Authorization Matrix)

Ký hiệu: **F** = Toàn quyền (thêm/sửa/xóa) · **R** = Chỉ xem · **Rown** = Chỉ xem dữ liệu của chính mình · **–** = Không được truy cập

| Chức năng | Bệnh nhân | Lễ tân | Nha sĩ | Quản lý |
|---|:---:|:---:|:---:|:---:|
| Đăng nhập / đổi mật khẩu | F | F | F | F |
| Hồ sơ bệnh nhân (đăng ký, cập nhật) | Rown | **F** | R | R |
| Tra cứu / xem lịch sử khám bệnh | Rown | R | R | R |
| Đặt lịch hẹn | F (của mình) | **F** | – | R |
| Hủy lịch hẹn | F (của mình) | **F** | – | R |
| Xem lịch hẹn | Rown | F | R | R |
| Xem lịch làm việc cá nhân | – | R | **R** | R |
| Khám bệnh, ghi hồ sơ điều trị | – | – | **F** | R |
| Tạo hóa đơn | – | **F** | – | R |
| Thanh toán / in hóa đơn | – | **F** | – | R |
| Quản lý danh mục dịch vụ (giá) | – | R | R | **F** |
| Báo cáo doanh thu, thống kê | – | – | – | **F** |

### Diễn giải theo vai trò

**Bệnh nhân**
- Chỉ xem **hồ sơ và lịch hẹn của chính mình** (kiểm tra `patient.id == session.userId` ở tầng Service, không chỉ ẩn nút trên giao diện).
- **Không** truy cập chức năng khám bệnh, hóa đơn, quản lý dịch vụ, báo cáo.

**Lễ tân**
- Toàn quyền: đăng ký/cập nhật hồ sơ bệnh nhân, đặt/hủy lịch hẹn, tạo hóa đơn, thanh toán.
- Không được sửa nội dung chẩn đoán trong `MedicalRecord`.

**Nha sĩ**
- Toàn quyền khám bệnh và ghi hồ sơ điều trị.
- **Chỉ xem** lịch hẹn và hồ sơ bệnh nhân; không đặt/hủy lịch, không tạo hóa đơn.

**Quản lý**
- Toàn quyền quản lý danh mục dịch vụ và xem báo cáo thống kê.
- **Xem** các chức năng còn lại (quyền giám sát, không thao tác nghiệp vụ hằng ngày).

### Cơ chế kỹ thuật
- Áp dụng **RBAC (Role-Based Access Control)**: `User (1) --- (1) Role (1) --- (0..*) Permission`.
- Kiểm tra quyền tại **tầng Service**, không phụ thuộc vào việc ẩn/hiện nút trên UI.
- Ghi **audit log** cho mọi thao tác truy cập và sửa hồ sơ bệnh án, hóa đơn (NFR-03).
