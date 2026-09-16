# Phần I – Phân tích hệ thống và Thu thập yêu cầu

## 1.1. Năm thành phần của Hệ thống thông tin (HTTT) DentCare

| Thành phần | Nội dung cụ thể tại DentCare |
|---|---|
| **Phần cứng (Hardware)** | Máy tính quầy lễ tân, máy tính/tablet phòng khám cho nha sĩ, máy chủ (hoặc VPS cloud), máy in hóa đơn nhiệt, máy quét mã, thiết bị mạng (router, switch), UPS |
| **Phần mềm (Software)** | Phần mềm quản lý phòng khám DentCare (web app), hệ quản trị CSDL (MySQL/PostgreSQL), hệ điều hành máy chủ, trình duyệt, cổng gửi SMS (SMS Gateway API), phần mềm sao lưu |
| **Dữ liệu (Data)** | Hồ sơ bệnh nhân (patientId, họ tên, ngày sinh, SĐT), lịch hẹn, hồ sơ khám bệnh (chẩn đoán, điều trị), danh mục dịch vụ nha khoa & đơn giá, hóa đơn và chi tiết hóa đơn, tài khoản người dùng & phân quyền |
| **Quy trình (Process)** | Quy trình đặt lịch hẹn, quy trình tiếp nhận – khám bệnh – ghi hồ sơ, quy trình lập hóa đơn & thanh toán, quy trình hủy lịch, quy trình kết sổ và báo cáo doanh thu theo ngày/tháng |
| **Con người (People)** | Bệnh nhân, Lễ tân, Nha sĩ, Quản lý phòng khám; ngoài ra có quản trị hệ thống (IT) và đội phát triển phần mềm |

### Phân loại hệ thống
DentCare thuộc loại **TPS – Transaction Processing System (Hệ thống xử lý giao dịch)**.

**Lý do:**
- Hệ thống ghi nhận và xử lý **các giao dịch nghiệp vụ lặp đi lặp lại hằng ngày** với khối lượng lớn: đặt lịch hẹn, tiếp nhận bệnh nhân, ghi hồ sơ khám, lập hóa đơn, thu tiền.
- Mỗi giao dịch có tính **nguyên tử** (thành công trọn vẹn hoặc hủy bỏ), đòi hỏi **độ chính xác và tính toàn vẹn dữ liệu cao** (không được trùng lịch, không được sai tiền).
- Dữ liệu đầu ra của TPS (hóa đơn, lượt khám) chính là **đầu vào cho MIS** – phần báo cáo doanh thu tổng hợp ở Phần VI mang tính chất MIS được xây dựng trên nền TPS.

---

## 1.2. Các bước SDLC cho dự án DentCare

| # | Giai đoạn SDLC | Hoạt động cụ thể tại DentCare | Sản phẩm đầu ra |
|---|---|---|---|
| 1 | **Planning – Lập kế hoạch** | Xác định vấn đề (sổ tay ghi tay, trùng lịch, không có báo cáo doanh thu), xác định phạm vi, ước lượng chi phí – thời gian, đánh giá tính khả thi | Project Charter, Feasibility Study |
| 2 | **Analysis – Phân tích yêu cầu** | Phỏng vấn lễ tân/nha sĩ/quản lý, khảo sát bệnh nhân, quan sát quy trình thủ công, nghiên cứu sổ sách hiện có; xác định yêu cầu chức năng & phi chức năng, viết User Story | SRS, User Story, Activity Diagram, Use Case Diagram |
| 3 | **Design – Thiết kế** | Thiết kế kiến trúc (3 lớp: UI – Service – CSDL), thiết kế CSDL, Class Diagram, Sequence Diagram, thiết kế giao diện, thiết kế phân quyền | Class Diagram, Sequence Diagram, ERD, Wireframe |
| 4 | **Implementation – Lập trình** | Code theo từng module: Quản lý bệnh nhân → Lịch hẹn → Khám bệnh → Hóa đơn → Báo cáo | Mã nguồn, CSDL |
| 5 | **Testing – Kiểm thử** | Unit test, Integration test, kiểm thử ràng buộc (trùng lịch, validation SĐT, tính tổng tiền), UAT với lễ tân và nha sĩ thật | Test case, biên bản UAT |
| 6 | **Deployment – Triển khai** | Cài đặt máy chủ, chuyển đổi dữ liệu từ sổ giấy sang hệ thống, đào tạo lễ tân/nha sĩ, chạy song song 2 tuần | Hệ thống vận hành, tài liệu hướng dẫn |
| 7 | **Maintenance – Bảo trì** | Sửa lỗi phát sinh, sao lưu định kỳ, bổ sung tính năng (đặt lịch qua app, nhắc hẹn Zalo) | Bản vá, phiên bản nâng cấp |

## 1.3. Lựa chọn mô hình phát triển: **Agile (Scrum)**

**Lý do chọn Agile thay vì Waterfall:**

1. **Yêu cầu chưa ổn định và sẽ thay đổi.** Phòng khám đang làm thủ công hoàn toàn, nhân viên chưa từng dùng phần mềm nên chưa hình dung được mình cần gì; yêu cầu sẽ được làm rõ dần sau mỗi lần dùng thử. Waterfall "đóng băng" yêu cầu ngay từ đầu là rất rủi ro.
2. **Cần có giá trị sử dụng sớm.** Có thể chia thành các Sprint bàn giao từng phân hệ chạy được: Sprint 1 – Quản lý bệnh nhân & Lịch hẹn (giải quyết ngay nỗi đau trùng lịch), Sprint 2 – Khám bệnh & Hồ sơ, Sprint 3 – Hóa đơn & Thanh toán, Sprint 4 – Báo cáo & Phân quyền.
3. **Người dùng cuối luôn sẵn sàng phản hồi.** Lễ tân, nha sĩ, quản lý làm việc ngay tại chỗ → dễ demo cuối Sprint và điều chỉnh nhanh.
4. **Dự án quy mô nhỏ – vừa, đội ít người**, không đòi hỏi tài liệu pháp lý nặng như dự án chính phủ/quốc phòng (nơi Waterfall phù hợp hơn).

> **Lưu ý:** Riêng phần **bảo mật hồ sơ bệnh nhân** và **quy tắc tính tiền** cần được đặc tả kỹ và cố định ngay từ đầu (áp dụng tư duy Waterfall cục bộ), vì đây là ràng buộc pháp lý và tài chính, không nên thay đổi tùy tiện qua từng Sprint.

---

## 1.4. Stakeholders và kỹ thuật thu thập yêu cầu

### a) Danh sách Stakeholder

| Stakeholder | Loại | Vai trò / Mối quan tâm |
|---|---|---|
| **Bệnh nhân** | Bên ngoài, người dùng trực tiếp | Đặt lịch nhanh, không phải chờ, xem được hồ sơ và lịch sử khám |
| **Lễ tân** | Bên trong, người dùng chính | Thao tác nhanh, không trùng lịch, in hóa đơn chính xác |
| **Nha sĩ** | Bên trong, người dùng chính | Xem lịch làm việc, ghi hồ sơ điều trị nhanh gọn, tra cứu lịch sử bệnh nhân |
| **Quản lý phòng khám** | Bên trong, người ra quyết định / nhà tài trợ | Doanh thu, hiệu suất, quản lý danh mục dịch vụ và giá |
| *(Bổ sung)* Quản trị hệ thống/IT | Hỗ trợ | Sao lưu, phân quyền, vận hành hạ tầng |

### b) Nguồn yêu cầu (Requirement Sources)

| Nguồn | Nội dung khai thác được |
|---|---|
| **Con người** | Phỏng vấn lễ tân, nha sĩ, quản lý; khảo sát bệnh nhân |
| **Hệ thống hiện tại** | Quy trình thủ công: sổ lịch hẹn viết tay, cách xếp khung giờ, cách ghi hồ sơ giấy, cách viết hóa đơn tay |
| **Tài liệu** | Mẫu hồ sơ bệnh án, mẫu hóa đơn, bảng giá dịch vụ nha khoa, quy định của Bộ Y tế về lưu trữ hồ sơ bệnh án, nội quy phòng khám |

### c) Kỹ thuật thu thập phù hợp cho từng Stakeholder

| Stakeholder | Kỹ thuật đề xuất | Vì sao phù hợp |
|---|---|---|
| **Bệnh nhân** | **Questionnaire/Survey** (bảng hỏi ngắn tại quầy hoặc online) + phỏng vấn nhanh 5–10 người | Số lượng đông, phân tán, thời gian tiếp xúc ngắn → cần công cụ thu thập diện rộng, chi phí thấp |
| **Lễ tân** | **Interview (phỏng vấn sâu)** + **Observation (quan sát tại chỗ)** một ca làm việc | Là người nắm quy trình chi tiết nhất; quan sát giúp phát hiện các thao tác ngầm mà họ quên kể (VD: cách xử lý khi bệnh nhân đến muộn) |
| **Nha sĩ** | **Interview** ngắn ngoài giờ khám + **Document Analysis** (mẫu hồ sơ bệnh án) | Nha sĩ rất bận, không thể họp dài; hồ sơ giấy đã chứa sẵn cấu trúc dữ liệu cần số hóa |
| **Quản lý** | **Interview** + **Workshop/JAD** để chốt phạm vi và độ ưu tiên, **Prototyping** cho màn hình báo cáo | Là người quyết định ngân sách & ưu tiên; prototype báo cáo giúp chốt nhanh chỉ tiêu cần xem |
| **Chung cả nhóm** | **Prototyping** demo cuối mỗi Sprint, **Brainstorming** khi thiết kế luồng đặt lịch | Phù hợp với mô hình Agile đã chọn |

---

## 1.5. Yêu cầu chức năng (Functional Requirements)

| Mã | Yêu cầu chức năng | Actor |
|---|---|---|
| FR-01 | Đăng nhập / đăng xuất, phân quyền theo vai trò | Tất cả |
| FR-02 | Đăng ký và cập nhật hồ sơ bệnh nhân | Lễ tân |
| FR-03 | Tra cứu bệnh nhân theo mã hoặc số điện thoại | Lễ tân, Nha sĩ, Quản lý |
| FR-04 | Xem lịch trống của nha sĩ theo ngày và khung giờ | Bệnh nhân, Lễ tân |
| FR-05 | Đặt lịch hẹn (chọn nha sĩ, ngày, khung giờ, dịch vụ dự kiến) | Bệnh nhân, Lễ tân |
| FR-06 | Xác nhận lịch hẹn | Lễ tân |
| FR-07 | Hủy lịch hẹn (chỉ khi trạng thái Chờ xác nhận / Đã xác nhận) | Bệnh nhân, Lễ tân |
| FR-08 | Gửi SMS nhắc hẹn / thông báo hủy lịch | Hệ thống |
| FR-09 | Xem lịch làm việc cá nhân | Nha sĩ |
| FR-10 | Ghi nhận hồ sơ khám bệnh: triệu chứng, chẩn đoán, điều trị, dịch vụ đã thực hiện | Nha sĩ |
| FR-11 | Xem lịch sử khám bệnh của bệnh nhân | Nha sĩ, Bệnh nhân (của mình) |
| FR-12 | Tạo hóa đơn từ hồ sơ khám bệnh, tự động tính tổng tiền | Lễ tân |
| FR-13 | Thanh toán hóa đơn (tiền mặt / chuyển khoản), in hóa đơn | Lễ tân |
| FR-14 | Quản lý danh mục dịch vụ nha khoa (thêm/sửa/xóa/đơn giá) | Quản lý |
| FR-15 | Xem báo cáo danh mục dịch vụ kèm đơn giá | Quản lý |
| FR-16 | Xem báo cáo doanh thu theo ngày/tháng (tổng lượt khám, tổng tiền) | Quản lý |

## 1.6. Yêu cầu phi chức năng (Non-Functional Requirements)

| Mã | Loại | Yêu cầu phi chức năng |
|---|---|---|
| NFR-01 | **Hiệu năng** | Thời gian phản hồi của thao tác tra cứu/đặt lịch **≤ 2 giây** với 50 người dùng đồng thời; truy vấn báo cáo doanh thu tháng ≤ 5 giây |
| NFR-02 | **Bảo mật** | Hồ sơ bệnh nhân là dữ liệu nhạy cảm: mật khẩu băm bằng bcrypt, truyền qua HTTPS/TLS; bệnh nhân chỉ xem được hồ sơ của chính mình |
| NFR-03 | **Bảo mật – Audit** | Ghi log mọi thao tác truy cập/sửa hồ sơ bệnh án và hóa đơn (ai, khi nào, làm gì) |
| NFR-04 | **Tính sẵn sàng** | Uptime ≥ 99% trong giờ hành chính (7h–20h); sao lưu CSDL tự động mỗi ngày, lưu 30 ngày |
| NFR-05 | **Tính khả dụng** | Giao diện tiếng Việt, lễ tân mới có thể đặt một lịch hẹn trong ≤ 3 phút sau 1 giờ đào tạo |
| NFR-06 | **Tính toàn vẹn** | Không cho phép 2 lịch hẹn "Đã xác nhận" trùng nha sĩ + ngày + khung giờ (ràng buộc unique ở tầng CSDL) |
| NFR-07 | **Khả năng bảo trì / mở rộng** | Kiến trúc 3 lớp, tách Service riêng; dễ bổ sung hình thức thanh toán mới (ví điện tử) mà không sửa lớp Invoice |
| NFR-08 | **Tương thích** | Chạy trên trình duyệt Chrome/Edge/Firefox phiên bản mới, responsive cho tablet của nha sĩ |
| NFR-09 | **Tuân thủ** | Lưu trữ hồ sơ bệnh án tối thiểu theo quy định ngành y tế; không xóa vật lý hồ sơ và hóa đơn |

---

## 1.7. User Story

> Cấu trúc: **"As a [role], I want [feature], so that [benefit]"**

### Nhóm Đặt lịch hẹn
- **US-01** – *Là Bệnh nhân*, tôi muốn xem khung giờ trống của nha sĩ và tự đặt lịch hẹn online, **để** tôi không phải gọi điện hay đến tận nơi chờ đợi.
  - *Tiêu chí chấp nhận:* Chỉ hiển thị khung giờ chưa có lịch "Đã xác nhận"; ngày hẹn không nhỏ hơn ngày hiện tại; đặt xong trạng thái = "Chờ xác nhận".
- **US-02** – *Là Lễ tân*, tôi muốn đặt lịch hẹn cho bệnh nhân, **để** sắp xếp lịch khám không bị trùng.
  - *Tiêu chí chấp nhận:* Hệ thống báo lỗi khi chọn khung giờ đã có lịch xác nhận của cùng nha sĩ trong cùng ngày.
- **US-03** – *Là Bệnh nhân*, tôi muốn hủy lịch hẹn của mình, **để** tôi chủ động khi có việc đột xuất.
  - *Tiêu chí chấp nhận:* Chỉ hủy được khi trạng thái là "Chờ xác nhận" hoặc "Đã xác nhận"; trạng thái "Đã khám" thì báo lỗi; nha sĩ được thông báo qua SMS.
- **US-11** – *Là Bệnh nhân*, tôi muốn nhận SMS nhắc hẹn trước giờ khám, **để** tôi không quên lịch.

### Nhóm Hồ sơ bệnh nhân
- **US-04** – *Là Lễ tân*, tôi muốn đăng ký và cập nhật hồ sơ bệnh nhân, **để** thông tin liên lạc và bệnh sử luôn chính xác.
  - *Tiêu chí chấp nhận:* SĐT đúng 10 chữ số bắt đầu bằng "0"; ngày sinh không lớn hơn ngày hiện tại.
- **US-10** – *Là Lễ tân*, tôi muốn tìm bệnh nhân theo mã hoặc số điện thoại, **để** tra cứu nhanh lịch sử khám và hóa đơn liên quan.

### Nhóm Khám bệnh
- **US-05** – *Là Nha sĩ*, tôi muốn ghi nhận chẩn đoán, phương pháp điều trị và các dịch vụ đã thực hiện vào hồ sơ điện tử, **để** lần khám sau có đủ căn cứ theo dõi và lễ tân có cơ sở lập hóa đơn.
- **US-12** – *Là Nha sĩ*, tôi muốn xem lịch làm việc theo ngày, **để** chủ động sắp xếp thời gian.

### Nhóm Thanh toán
- **US-06** – *Là Lễ tân*, tôi muốn tạo hóa đơn tự động từ hồ sơ khám bệnh, **để** không phải cộng tiền thủ công và tránh sai sót.
  - *Tiêu chí chấp nhận:* Chỉ tạo được hóa đơn sau khi hồ sơ khám đã hoàn tất; totalAmount = Σ lineTotal.
- **US-07** – *Là Lễ tân*, tôi muốn ghi nhận thanh toán bằng tiền mặt hoặc chuyển khoản và in hóa đơn, **để** hoàn tất giao dịch cho bệnh nhân.

### Nhóm Quản trị & Báo cáo
- **US-08** – *Là Quản lý*, tôi muốn thêm/sửa/xóa dịch vụ nha khoa và đơn giá, **để** bảng giá luôn cập nhật.
  - *Tiêu chí chấp nhận:* Đơn giá phải > 0.
- **US-09** – *Là Quản lý*, tôi muốn xem báo cáo doanh thu theo ngày/tháng gồm tổng lượt khám và tổng tiền, **để** đánh giá hiệu quả kinh doanh của phòng khám.
- **US-13** – *Là Quản lý*, tôi muốn xem danh sách toàn bộ dịch vụ kèm đơn giá, **để** kiểm soát danh mục đang cung cấp.
