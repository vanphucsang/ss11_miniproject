# Phần VI – Hiển thị và Báo cáo

## 6.1. Báo cáo danh mục dịch vụ (tồn kho dịch vụ)

**Mục đích:** Hiển thị danh sách toàn bộ dịch vụ nha khoa kèm đơn giá.
**Người dùng:** Quản lý (toàn quyền), Lễ tân / Nha sĩ (chỉ xem).

**Bộ lọc:** Tên dịch vụ (từ khóa), Trạng thái (Đang cung cấp / Ngừng), Khoảng đơn giá.

| STT | Mã DV | Tên dịch vụ | Mô tả | Đơn vị tính | Đơn giá (VNĐ) | Trạng thái |
|---|---|---|---|---|---:|---|
| 1 | DV001 | Khám tổng quát | Khám và tư vấn răng miệng | Lượt | 100.000 | Đang cung cấp |
| 2 | DV002 | Cạo vôi răng | Làm sạch cao răng 2 hàm | Lượt | 300.000 | Đang cung cấp |
| 3 | DV003 | Trám răng composite | Trám răng sâu | Răng | 350.000 | Đang cung cấp |
| 4 | DV004 | Nhổ răng khôn | Tiểu phẫu răng số 8 | Răng | 1.500.000 | Đang cung cấp |
| 5 | DV005 | Điều trị tủy | Chữa tủy răng | Răng | 1.200.000 | Đang cung cấp |
| 6 | DV006 | Bọc răng sứ | Răng sứ Titan | Răng | 2.500.000 | Đang cung cấp |
| 7 | DV007 | Tẩy trắng răng | Tẩy trắng bằng đèn Laser | Lượt | 2.000.000 | Đang cung cấp |
| | | | | **Tổng số dịch vụ** | **7** | |

---

## 6.2. Báo cáo doanh thu theo kỳ

**Mục đích:** Tổng hợp doanh thu theo ngày/tháng, hiển thị **tổng số lượt khám** và **tổng tiền thu được**.
**Người dùng:** Quản lý.

**Tham số đầu vào:** Loại kỳ (Ngày / Tháng / Khoảng tùy chọn), Từ ngày – Đến ngày, (tùy chọn) lọc theo Nha sĩ.
**Nguồn dữ liệu:** `Invoice` (status = "Đã thanh toán") join `InvoiceDetail`, `MedicalRecord`, `Appointment`.

### a) Báo cáo theo ngày — *Kỳ: 01/09/2026 – 05/09/2026*

| Ngày | Số lượt khám | Số hóa đơn | Tổng tiền (VNĐ) |
|---|---:|---:|---:|
| 01/09/2026 | 12 | 12 | 8.450.000 |
| 02/09/2026 | 9 | 9 | 5.200.000 |
| 03/09/2026 | 15 | 14 | 11.750.000 |
| 04/09/2026 | 7 | 7 | 3.900.000 |
| 05/09/2026 | 11 | 11 | 9.300.000 |
| **TỔNG** | **54** | **53** | **38.600.000** |

### b) Báo cáo theo tháng — *Năm 2026*

| Tháng | Số lượt khám | Tổng doanh thu (VNĐ) | Doanh thu TB/lượt |
|---|---:|---:|---:|
| 07/2026 | 210 | 152.300.000 | 725.238 |
| 08/2026 | 245 | 181.750.000 | 741.837 |
| 09/2026 | 198 | 143.900.000 | 726.767 |
| **TỔNG** | **653** | **477.950.000** | **731.930** |

### c) Top dịch vụ theo doanh thu (bổ sung cho Quản lý)

| Hạng | Dịch vụ | Số lượt | Doanh thu (VNĐ) | Tỷ trọng |
|---|---|---:|---:|---:|
| 1 | Bọc răng sứ | 42 | 105.000.000 | 22,0% |
| 2 | Điều trị tủy | 58 | 69.600.000 | 14,6% |
| 3 | Nhổ răng khôn | 40 | 60.000.000 | 12,6% |
| 4 | Tẩy trắng răng | 25 | 50.000.000 | 10,5% |
| 5 | Trám răng composite | 96 | 33.600.000 | 7,0% |

**Công thức nghiệp vụ:**
- `Tổng số lượt khám` = COUNT(DISTINCT `MedicalRecord.recordId`) trong kỳ.
- `Tổng tiền thu được` = SUM(`Invoice.totalAmount`) với `Invoice.status = 'Đã thanh toán'` và `issueDate` thuộc kỳ.
- Chỉ tính hóa đơn **đã thanh toán**; hóa đơn chưa thu được tách riêng thành mục **Công nợ**.

---

## 6.3. Tra cứu hồ sơ bệnh nhân

**Mục đích:** Tìm kiếm bệnh nhân theo **mã** hoặc **số điện thoại**, hiển thị lịch sử khám bệnh và hóa đơn liên quan.
**Người dùng:** Lễ tân, Nha sĩ, Quản lý (chỉ xem); Bệnh nhân chỉ tra cứu hồ sơ của chính mình.

**Bước 1 – Tìm kiếm:** nhập `patientId` hoặc `phoneNumber` (10 số, bắt đầu bằng 0).

**Bước 2 – Thông tin bệnh nhân**

| Mã BN | Họ tên | Ngày sinh | Giới tính | Điện thoại | Địa chỉ |
|---|---|---|---|---|---|
| BN0012 | Nguyễn Văn A | 15/04/1995 | Nam | 0901234567 | Quận 3, TP.HCM |

**Bước 3 – Lịch sử khám bệnh**

| Mã HS | Ngày khám | Nha sĩ | Chẩn đoán | Điều trị | Mã hóa đơn |
|---|---|---|---|---|---|
| HS0031 | 12/03/2026 | BS. Trần B | Sâu răng số 36 | Trám composite | HD0031 |
| HS0057 | 20/06/2026 | BS. Lê C | Viêm tủy răng 46 | Điều trị tủy | HD0057 |
| HS0090 | 02/09/2026 | BS. Trần B | Vôi răng độ 2 | Cạo vôi 2 hàm | HD0090 |

**Bước 4 – Hóa đơn liên quan**

| Mã HĐ | Ngày lập | Dịch vụ sử dụng | Tổng tiền (VNĐ) | Hình thức | Trạng thái |
|---|---|---|---:|---|---|
| HD0031 | 12/03/2026 | Khám tổng quát, Trám composite | 450.000 | Tiền mặt | Đã thanh toán |
| HD0057 | 20/06/2026 | Khám tổng quát, Điều trị tủy | 1.300.000 | Chuyển khoản | Đã thanh toán |
| HD0090 | 02/09/2026 | Cạo vôi răng | 300.000 | Tiền mặt | Đã thanh toán |
| | | **Tổng chi tiêu** | **2.050.000** | | |

**Bước 5 – Lịch hẹn sắp tới**

| Mã LH | Ngày hẹn | Khung giờ | Nha sĩ | Trạng thái |
|---|---|---|---|---|
| LH0125 | 25/09/2026 | 09:00–09:30 | BS. Trần B | Đã xác nhận |

---

## 6.4. Nguyên tắc hiển thị chung
- Tiền tệ định dạng VNĐ có dấu phân cách hàng nghìn; ngày tháng `dd/MM/yyyy`.
- Danh sách dài được **phân trang** (20 dòng/trang) và cho phép **xuất Excel/PDF**.
- Mọi báo cáo đều hiển thị **kỳ báo cáo**, **người xem** và **thời điểm kết xuất**.
- Dữ liệu nhạy cảm (chẩn đoán, hóa đơn) chỉ hiển thị theo đúng ma trận phân quyền ở Phần V.
