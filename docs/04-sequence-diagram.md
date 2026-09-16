# Phần IV – Mô hình hóa tương tác (Sequence Diagram)

## 4.1. SD-01 – Luồng **Đặt lịch hẹn**

```mermaid
sequenceDiagram
    actor BenhNhan as :BenhNhan
    participant UI as :DatLichUI
    participant AS as :AppointmentService
    participant DS as :DentistService
    participant DB as :CSDL

    BenhNhan->>UI: chonDatLich(dentistId, ngayHen)
    activate UI
    UI->>DS: layLichTrong(dentistId, ngayHen)
    activate DS
    DS->>DB: SELECT * FROM Appointment<br/>WHERE dentistId AND date AND status='Đã xác nhận'
    activate DB
    DB-->>DS: danhSachLichDaDat
    deactivate DB
    DS-->>UI: danhSachKhungGioTrong
    deactivate DS
    UI-->>BenhNhan: hienThiKhungGioTrong()

    BenhNhan->>UI: chonKhungGio(timeSlot) + xacNhanDatLich()
    UI->>AS: datLichHen(patientId, dentistId, ngayHen, timeSlot)
    activate AS
    AS->>AS: validateNgayHen(ngayHen)
    AS->>DS: kiemTraTrungLich(dentistId, ngayHen, timeSlot)
    activate DS
    DS->>DB: SELECT COUNT(*) ... status='Đã xác nhận'
    activate DB
    DB-->>DS: soLuong
    deactivate DB
    DS-->>AS: coTheDat : boolean
    deactivate DS

    alt Còn slot trống (coTheDat == true)
        AS->>DB: INSERT INTO Appointment<br/>(status='Chờ xác nhận')
        activate DB
        DB-->>AS: appointmentId
        deactivate DB
        AS-->>UI: ketQua(thanhCong, appointmentId)
        UI-->>BenhNhan: thongBaoDatLichThanhCong(appointmentId)
    else else (đã có lịch trùng)
        AS->>DS: deXuatKhungGioKhac(dentistId, ngayHen)
        activate DS
        DS-->>AS: danhSachGoiY
        deactivate DS
        AS-->>UI: ketQua(thatBai, danhSachGoiY)
        UI-->>BenhNhan: thongBaoTrungLich() + hienThiGoiY()
    end
    deactivate AS
    deactivate UI
```

**Ký pháp đã sử dụng:**
- **Thông điệp đồng bộ** (mũi tên đặc `->>` + Activation Bar): `layLichTrong()`, `kiemTraTrungLich()` – bên gửi **chờ** kết quả trả về.
- **Return Message nét đứt** (`-->>`): `danhSachKhungGioTrong`, `coTheDat`, `appointmentId`.
- **Combined Fragment `alt`**: hai nhánh `[Còn slot trống]` / `[else]`.
- **Self Message**: `AS->>AS: validateNgayHen()` – đối tượng tự gọi phương thức của chính nó.

> PlantUML: [`diagrams/puml/sequence-dat-lich-hen.puml`](../diagrams/puml/sequence-dat-lich-hen.puml)

---

## 4.2. SD-02 – Luồng **Khám bệnh và Tạo hóa đơn**

```mermaid
sequenceDiagram
    actor NhaSi as :NhaSi
    participant UI as :KhamBenhUI
    participant RS as :RecordService
    participant IS as :InvoiceService
    participant DB as :CSDL

    NhaSi->>UI: moHoSoKham(appointmentId)
    activate UI
    UI->>RS: taoHoSoKham(appointmentId)
    activate RS
    RS->>DB: INSERT INTO MedicalRecord(...)
    activate DB
    DB-->>RS: recordId
    deactivate DB
    RS-->>UI: hoSoKham(recordId)
    deactivate RS

    NhaSi->>UI: nhapChanDoan(symptom, diagnosis, treatment)
    NhaSi->>UI: chonDichVuDaThucHien(danhSachDichVu)
    UI->>RS: capNhatHoSo(recordId, chanDoan, danhSachDichVu)
    activate RS
    RS->>DB: UPDATE MedicalRecord SET ...
    activate DB
    DB-->>RS: OK
    deactivate DB
    RS->>RS: hoanTatHoSo(recordId)
    RS-->>UI: hoSoHoanTat(recordId)
    deactivate RS

    UI->>IS: taoHoaDon(recordId)
    activate IS
    IS->>DB: SELECT dichVu FROM MedicalRecord WHERE recordId
    activate DB
    DB-->>IS: danhSachDichVu
    deactivate DB
    IS->>DB: INSERT INTO Invoice(status='Chưa thanh toán')
    activate DB
    DB-->>IS: invoiceId
    deactivate DB

    loop cho mỗi dịch vụ đã thực hiện
        IS->>IS: tinhLineTotal(unitPrice, quantity)
        IS->>DB: INSERT INTO InvoiceDetail(invoiceId, serviceId, quantity, lineTotal)
        activate DB
        DB-->>IS: detailId
        deactivate DB
    end

    IS->>IS: tinhTongTien()  %% totalAmount = Σ lineTotal
    IS->>DB: UPDATE Invoice SET totalAmount
    activate DB
    DB-->>IS: OK
    deactivate DB
    IS-->>UI: hoaDon(invoiceId, totalAmount)
    deactivate IS
    UI-->>NhaSi: hienThiHoaDon(invoiceId, totalAmount)

    opt Bệnh nhân yêu cầu in hóa đơn
        NhaSi->>UI: yeuCauInHoaDon(invoiceId)
        UI->>IS: inHoaDon(invoiceId)
        activate IS
        IS-->>UI: fileHoaDon
        deactivate IS
        UI-->>NhaSi: xuatBanIn()
    end
    deactivate UI
```

**Ký pháp đã sử dụng:**
- **`loop [cho mỗi dịch vụ đã thực hiện]`**: thêm từng `InvoiceDetail`.
- **`opt [Bệnh nhân yêu cầu in hóa đơn]`**: nhánh tùy chọn.
- **Self Message `tinhTongTien()`**: `InvoiceService` tự tính `totalAmount = Σ lineTotal` (ràng buộc nghiệp vụ).
- **Thứ tự bắt buộc:** `taoHoaDon()` chỉ được gọi **sau** khi `hoanTatHoSo()` trả về thành công → hiện thực ràng buộc *"Hóa đơn chỉ được tạo sau khi Nha sĩ hoàn tất ghi nhận MedicalRecord"*.

> PlantUML: [`diagrams/puml/sequence-kham-benh-tao-hoadon.puml`](../diagrams/puml/sequence-kham-benh-tao-hoadon.puml)

---

## 4.3. SD-03 – Luồng **Hủy lịch hẹn**

```mermaid
sequenceDiagram
    actor BenhNhan as :BenhNhan
    participant UI as :HuyLichUI
    participant AS as :AppointmentService
    participant DB as :CSDL
    participant SMS as :SMSGateway

    BenhNhan->>UI: chonHuyLich(appointmentId)
    activate UI
    UI->>AS: huyLichHen(appointmentId)
    activate AS
    AS->>DB: SELECT status FROM Appointment WHERE appointmentId
    activate DB
    DB-->>AS: trangThai
    deactivate DB
    AS->>AS: kiemTraDieuKienHuy(trangThai)

    alt Trạng thái == "Chờ xác nhận" hoặc "Đã xác nhận"
        AS->>DB: UPDATE Appointment SET status='Đã hủy'
        activate DB
        DB-->>AS: OK
        deactivate DB
        AS-)SMS: guiSMSThongBao(dentistId, noiDung)
        Note right of SMS: Thông điệp BẤT ĐỒNG BỘ<br/>không chờ phản hồi
        AS-->>UI: ketQua(thanhCong)
        UI-->>BenhNhan: thongBaoHuyThanhCong()
    else else (Trạng thái == "Đã khám" hoặc "Đã hủy")
        AS-->>UI: ketQua(loi, "Lịch hẹn đã khám không thể hủy")
        UI-->>BenhNhan: hienThiLoi()
    end
    deactivate AS
    deactivate UI
```

**Ký pháp đã sử dụng:**
- **Thông điệp bất đồng bộ** (`-)`, mũi tên nét mảnh hở): `guiSMSThongBao()` gửi tới `:SMSGateway` – hệ thống **không chờ** kết quả, người dùng nhận phản hồi ngay.
- **`alt [Trạng thái == Chờ xác nhận / Đã xác nhận]` / `[else]`**: hiện thực ràng buộc **BR-02**.
- **Self Message** `kiemTraDieuKienHuy()`.
- **Return Message nét đứt** cho mọi giá trị trả về.

> PlantUML: [`diagrams/puml/sequence-huy-lich-hen.puml`](../diagrams/puml/sequence-huy-lich-hen.puml)
