# Test Cases — Bảng trường hợp kiểm thử

> **Hướng dẫn**: Viết tối thiểu **20 TC** phủ đủ các chức năng chính (REQ-01 → REQ-08).
> Xem [examples/sample-test-case.md](../examples/sample-test-case.md) để hiểu cách viết TC tốt.
> Tự tổ chức và phân nhóm test case theo cách hợp lý nhất.

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 11 |
| **Ngày tạo** | 27/05/2026 |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tham chiếu** | SRS v1.0 |

---

## Bước 1: Mô hình hóa miền đầu vào — Input Domain Modeling (IDM)

> 📖 **Textbook:** Chương 6 — *Input Domain Modeling*, Paul Ammann & Jeff Offutt.
>
> **Trước khi viết Test Case**, nhóm **phải** phân tích miền đầu vào bằng bảng IDM bên dưới.
> Mỗi chức năng cần xác định: **Đặc tính (Characteristic)**, **Phân vùng (Block)**, và **Giá trị đại diện (Value)**.

### IDM — Đăng nhập (REQ-01)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Email có tồn tại trong DB? | Có | `librarian@library.com` | Đăng nhập thành công |
| | Không | `noone@email.com` | Thông báo "Không tìm thấy thành viên" |
| Mật khẩu có đúng? | Đúng | `admin123` | Đăng nhập thành công |
| | Sai | `wrongpass` | Thông báo "Mật khẩu không đúng" |
| Ô nhập có rỗng? | Không rỗng | (giá trị bất kỳ) | Xử lý bình thường |
| | Rỗng | `""` | Thông báo "Vui lòng nhập email và mật khẩu" |

### IDM — Xem danh sách sách (REQ-02)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Vai trò người dùng? | Thủ thư | `librarian@library.com` | Xem được danh sách sách đầy đủ |
| | Thành viên | `ba.nguyen@email.com` | Xem được danh sách sách đầy đủ |
| Cập nhật trạng thái real-time? | Sau khi mượn | Mượn BOOK001 → kiểm tra danh sách | BOOK001 chuyển trạng thái "Đang mượn" |
| | Sau khi trả | Trả BOOK003 → kiểm tra danh sách | BOOK003 chuyển trạng thái "Có sẵn" |

### IDM — Tìm kiếm và lọc sách (REQ-03)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Từ khóa có tồn tại trong DB? | Có (tên sách) | `"Flutter"` | Hiển thị sách chứa "Flutter" |
| | Có (tên tác giả) | `"Nguyễn"` | Hiển thị sách của tác giả Nguyễn |
| | Không | `"XYZ123"` | Danh sách rỗng, thông báo "Không tìm thấy sách" |
| Phân biệt HOA/thường? | Chữ thường | `"flutter"` | Kết quả giống "Flutter" (case-insensitive) |
| | Chữ HOA | `"FLUTTER"` | Kết quả giống "Flutter" (case-insensitive) |
| Lọc theo thể loại? | Thể loại có sách | `"Công nghệ"` | Hiển thị sách thể loại Công nghệ |
| | Thể loại — kiểm tra hoa/thường | `"công nghệ"` | Kết quả giống "Công nghệ" (case-insensitive theo SRS) |

### IDM — Mượn sách (REQ-04)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Trạng thái sách? | Có sẵn | BOOK001 | Cho phép mượn |
| | Đang mượn | BOOK003 | Từ chối, thông báo sách không có sẵn |
| | Thất lạc | BOOK007 | Từ chối, thông báo sách không có sẵn |
| Trạng thái thành viên? | Hoạt động | MEM002 | Cho phép mượn |
| | Tạm ngưng | MEM004 | Từ chối, thông báo chứa "tạm ngưng" |
| | Hết hạn | MEM005 | Từ chối, thông báo chứa "hết hạn" |
| Số sách đang mượn? | < 3 (BVA: 2) | Thành viên đang mượn 2 sách | Cho phép mượn |
| | = 3 (BVA: giới hạn) | Thành viên đang mượn 3 sách | Từ chối, thông báo vượt giới hạn |

### IDM — Trả sách (REQ-05)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Sách đang mượn bởi thành viên? | Đang mượn | BR003 (MEM006 mượn BOOK013) | Trả thành công, sách → "Có sẵn" |
| | Không mượn | Sách đã trả (BR002) | Không cho trả |
| Trả quá hạn? | Quá hạn (dueDate ≤ hôm nay) | BR001 (MEM002, hết hạn 15/09/2024) | Hiển thị cảnh báo quá hạn + trả thành công |
| | Đúng hạn | Sách chưa hết hạn | Trả thành công, không cảnh báo |

### IDM — Xử lý sách quá hạn (REQ-06)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Phiếu có quá hạn? | Có (dueDate ≤ hôm nay) | BR001 (hết hạn 15/09/2024) | Đánh dấu "Quá hạn" sau khi Thủ thư nhấn kiểm tra |
| | Không (dueDate > hôm nay) | BR003 (hết hạn 15/10/2024 — còn hạn so với hiện tại nếu hệ thống dùng ngày mô phỏng) | Không đánh dấu quá hạn |
| Ai kiểm tra? | Thủ thư | `librarian@library.com` | Xem tất cả phiếu quá hạn |
| | Thành viên | `ba.nguyen@email.com` | Chỉ xem phiếu quá hạn của mình |

### IDM — Quản lý thành viên (REQ-07)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Email hợp lệ? | Hợp lệ (có `@` VÀ `.` trong domain) | `newmember@email.com` | Tạo thành công |
| | Không hợp lệ — thiếu dấu chấm domain | `user@domain` | Thông báo lỗi email không hợp lệ |
| | Không hợp lệ — thiếu `@` | `userdomain.com` | Thông báo lỗi email không hợp lệ |
| Email đã tồn tại? | Chưa tồn tại | `newperson@test.com` | Tạo thành công |
| | Đã tồn tại | `ba.nguyen@email.com` | Thông báo lỗi email đã tồn tại |
| Thông tin bắt buộc? | Đầy đủ | Họ tên, email, SĐT | Tạo thành công |
| | Thiếu họ tên | Không nhập họ tên | Thông báo lỗi |

### IDM — Tra cứu phiếu mượn (REQ-08)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Vai trò xem phiếu? | Thủ thư | `librarian@library.com` | Xem tất cả phiếu mọi thành viên |
| | Thành viên | `ba.nguyen@email.com` | Chỉ xem phiếu của mình |
| Thành viên xem phiếu người khác? | Xem phiếu mình | MEM002 xem BR001, BR004 | Hiển thị |
| | Xem phiếu người khác | MEM002 không xem được BR003 (MEM006) | Không hiển thị phiếu của người khác |
| Thông tin mỗi phiếu? | Hiển thị đủ | BR001 | Mã phiếu, sách mượn, ngày mượn, hạn trả, trạng thái |

---

## Bước 2: Bảng quyết định — Decision Table (REQ-04: Mượn sách)

> 📖 **Bonus B2**: Bảng Decision Table hoàn chỉnh cho chức năng Mượn sách.
> 3 điều kiện kết hợp: Trạng thái sách × Trạng thái thành viên × Số sách đang mượn.

### Điều kiện (Conditions)

| Mã | Điều kiện | Giá trị |
|----|-----------|---------|
| **C1** | Trạng thái sách | Có sẵn (Y) / Không có sẵn (N: Đang mượn hoặc Thất lạc) |
| **C2** | Trạng thái thành viên | Hoạt động (Y) / Không hoạt động (N: Tạm ngưng hoặc Hết hạn) |
| **C3** | Số sách đang mượn < 3? | Có (Y) / Không (N) |

### Bảng quyết định đầy đủ (Full Decision Table)

| Quy tắc | C1: Sách Có sẵn? | C2: TV Hoạt động? | C3: Sách < 3? | Kết quả | Thông báo lỗi |
|---------|:-:|:-:|:-:|:--------:|---------------|
| **R1** | Y | Y | Y | ✅ Cho mượn | — |
| **R2** | Y | Y | N | ❌ Từ chối | Đã đạt giới hạn mượn tối đa (3 sách) |
| **R3** | Y | N (Tạm ngưng) | Y | ❌ Từ chối | Thành viên đang bị tạm ngưng. Không thể mượn sách. |
| **R4** | Y | N (Tạm ngưng) | N | ❌ Từ chối | Thành viên đang bị tạm ngưng. Không thể mượn sách. |
| **R5** | Y | N (Hết hạn) | Y | ❌ Từ chối | Thành viên đã hết hạn. Không thể mượn sách. |
| **R6** | Y | N (Hết hạn) | N | ❌ Từ chối | Thành viên đã hết hạn. Không thể mượn sách. |
| **R7** | N (Đang mượn) | Y | Y | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R8** | N (Đang mượn) | Y | N | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R9** | N (Đang mượn) | N (Tạm ngưng) | Y | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R10** | N (Đang mượn) | N (Tạm ngưng) | N | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R11** | N (Đang mượn) | N (Hết hạn) | Y | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R12** | N (Đang mượn) | N (Hết hạn) | N | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R13** | N (Thất lạc) | Y | Y | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R14** | N (Thất lạc) | Y | N | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R15** | N (Thất lạc) | N (Tạm ngưng) | Y | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R16** | N (Thất lạc) | N (Tạm ngưng) | N | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R17** | N (Thất lạc) | N (Hết hạn) | Y | ❌ Từ chối | Sách không có sẵn để mượn. |
| **R18** | N (Thất lạc) | N (Hết hạn) | N | ❌ Từ chối | Sách không có sẵn để mượn. |

### Ánh xạ Quy tắc → Test Case

| Quy tắc | TC tương ứng | Ghi chú |
|---------|-------------|---------|
| R1 | TC-12 | Happy path |
| R2 | TC-17 | BVA boundary |
| R3 | TC-15 | Suspended member |
| R5 | TC-16 | Expired member |
| R7 | TC-13 | Borrowed book |
| R13 | TC-14 | Lost book |
| R4, R6, R9–R12, R14–R18 | Kết quả trùng với R3/R5/R7/R13 — không cần TC riêng | Điều kiện mạnh hơn đã bao phủ |

> **Ghi chú**: Quy tắc R4, R6, R9–R18 có kết quả trùng với các quy tắc đã test (R3, R5, R7, R13). Khi sách không có sẵn hoặc thành viên không hoạt động, lý do từ chối do điều kiện "mạnh hơn" (được kiểm tra trước trong code) sẽ hiển thị. Do đó chỉ cần 6 TC để bao phủ 18 quy tắc có kết quả duy nhất.

---

## Bước 3: Test Cases

### REQ-01: Đăng nhập

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-01 | Đăng nhập thành công — Thủ thư | Trang đăng nhập đã mở, dữ liệu đã reset | 1. Nhập email `librarian@library.com`. 2. Nhập mật khẩu `admin123`. 3. Nhấn nút **Đăng nhập**. | Email: `librarian@library.com`, MK: `admin123` | Chuyển sang trang chủ. Thanh AppBar hiển thị tên "Nguyễn Thủ Thư" và vai trò "Thủ thư". | REQ-01 | EP |
| TC-02 | Đăng nhập thất bại — Email không tồn tại | Trang đăng nhập đã mở | 1. Nhập email `nobody@test.com`. 2. Nhập mật khẩu `anything`. 3. Nhấn nút **Đăng nhập**. | Email: `nobody@test.com`, MK: `anything` | Không chuyển trang. Hiển thị thông báo lỗi "Không tìm thấy thành viên". | REQ-01 | EP |
| TC-03 | Đăng nhập thất bại — Sai mật khẩu | Trang đăng nhập đã mở | 1. Nhập email `ba.nguyen@email.com`. 2. Nhập mật khẩu `wrongpassword`. 3. Nhấn nút **Đăng nhập**. | Email: `ba.nguyen@email.com`, MK: `wrongpassword` | Không chuyển trang. Hiển thị thông báo lỗi "Mật khẩu không đúng". | REQ-01 | EP |
| TC-04 | Đăng nhập thất bại — Bỏ trống cả 2 ô | Trang đăng nhập đã mở | 1. Để trống ô email. 2. Để trống ô mật khẩu. 3. Nhấn nút **Đăng nhập**. | Email: `""`, MK: `""` | Không chuyển trang. Hiển thị thông báo lỗi "Vui lòng nhập email và mật khẩu". | REQ-01 | EP |

### REQ-02: Xem danh sách sách

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-05 | Xem danh sách sách — Cả hai vai trò đều xem được | Đã đăng nhập (thử lần lượt Thủ thư và Thành viên) | 1. Đăng nhập Thủ thư → chuyển đến tab "Sách". 2. Đăng xuất. 3. Đăng nhập Thành viên (`ba.nguyen@email.com`) → chuyển đến tab "Sách". | N/A | Cả 2 lần đều hiển thị danh sách 20 sách. Mỗi sách hiển thị: tên, tác giả, thể loại, năm xuất bản, trạng thái (Có sẵn / Đang mượn / Thất lạc). | REQ-02 | EP |
| TC-06 | Cập nhật trạng thái sách real-time sau khi mượn | Đã đăng nhập bằng MEM002 (`ba.nguyen@email.com`). Dữ liệu đã reset. | 1. Chuyển đến tab "Sách". Ghi nhận BOOK001 trạng thái "Có sẵn". 2. Mượn sách BOOK001. 3. Quay lại tab "Sách". Kiểm tra trạng thái BOOK001. | Sách: BOOK001 | Sau bước 2: BOOK001 chuyển trạng thái "Đang mượn". Cập nhật hiển thị ngay lập tức trên danh sách mà không cần refresh. | REQ-02 | EP |

### REQ-03: Tìm kiếm và lọc sách

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-07 | Tìm kiếm theo tên sách — Có kết quả | Đã đăng nhập, đang ở tab "Sách" | 1. Nhập từ khóa `"Flutter"` vào ô tìm kiếm. | Từ khóa: `"Flutter"` | Hiển thị sách "Lập trình Flutter cơ bản" (BOOK001, Nguyễn Minh Đức, Công nghệ, 2023). | REQ-03 | EP |
| TC-08 | Tìm kiếm theo tác giả — Có kết quả | Đã đăng nhập, đang ở tab "Sách" | 1. Nhập từ khóa `"Nguyễn Minh Đức"` vào ô tìm kiếm. | Từ khóa: `"Nguyễn Minh Đức"` | Hiển thị 2 sách của tác giả Nguyễn Minh Đức: BOOK001 (Lập trình Flutter cơ bản) và BOOK009 (Nhập môn lập trình Python). | REQ-03 | EP |
| TC-09 | Tìm kiếm — Không có kết quả | Đã đăng nhập, đang ở tab "Sách" | 1. Nhập từ khóa `"xyz_khong_ton_tai"` vào ô tìm kiếm. | Từ khóa: `"xyz_khong_ton_tai"` | Danh sách rỗng. Hiển thị thông báo "Không tìm thấy sách". | REQ-03 | EP |
| TC-10 | Tìm kiếm không phân biệt hoa/thường (case-insensitive) | Đã đăng nhập, đang ở tab "Sách" | 1. Nhập từ khóa `"flutter"` (chữ thường) vào ô tìm kiếm. 2. Xóa, nhập `"FLUTTER"` (chữ HOA) vào ô tìm kiếm. | Từ khóa: `"flutter"`, `"FLUTTER"` | Cả 2 lần đều hiển thị kết quả giống `"Flutter"` — sách BOOK001 (Lập trình Flutter cơ bản). Tìm kiếm KHÔNG phân biệt chữ hoa/thường theo SRS REQ-03. | REQ-03 | BVA |
| TC-11 | Lọc sách theo thể loại | Đã đăng nhập, đang ở tab "Sách" | 1. Chọn thể loại "Công nghệ" trong bộ lọc. | Thể loại: `"Công nghệ"` | Hiển thị 8 sách thể loại Công nghệ: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011. | REQ-03 | EP |

### REQ-04: Mượn sách

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-12 | Mượn sách thành công — Sách Có sẵn, TV Hoạt động (DT-R1) | Đã đăng nhập bằng MEM002 (`ba.nguyen@email.com`, Hoạt động). Dữ liệu đã reset. | 1. Chuyển đến tab "Sách". 2. Chọn sách BOOK004 (Quản trị dự án phần mềm, Có sẵn). 3. Nhấn nút **Mượn**. | TV: MEM002, Sách: BOOK004 | Mượn thành công. Tạo phiếu mượn mới, hạn trả = ngày mượn + 14 ngày. Sách BOOK004 chuyển trạng thái "Đang mượn". Số sách đang mượn của MEM002 tăng lên 2. | REQ-04 | EP, DT-R1 |
| TC-13 | Mượn sách đã được mượn — Từ chối (DT-R7) | Đã đăng nhập. Dữ liệu đã reset (BOOK003 đang mượn bởi MEM002). | 1. Chuyển đến tab "Sách". 2. Tìm sách BOOK003 (trạng thái "Đang mượn"). 3. Nhấn nút **Mượn**. | Sách: BOOK003 (Đang mượn) | Không cho mượn. Hiển thị thông báo lỗi mô tả sách không có sẵn để mượn. | REQ-04 | EP, DT-R7 |
| TC-14 | Mượn sách Thất lạc — Từ chối (DT-R13) | Đã đăng nhập. Dữ liệu đã reset (BOOK007 trạng thái "Thất lạc"). | 1. Chuyển đến tab "Sách". 2. Tìm sách BOOK007 (Kinh tế vi mô, Thất lạc). 3. Nhấn nút **Mượn**. | Sách: BOOK007 (Thất lạc) | Không cho mượn. Hiển thị thông báo lỗi mô tả sách không có sẵn để mượn. | REQ-04 | EP, DT-R13 |
| TC-15 | Thành viên Tạm ngưng mượn sách — Từ chối (DT-R3) | Đã đăng nhập bằng tài khoản Thủ thư. Dữ liệu đã reset. | 1. Chuyển đến tab "Sách". 2. Chọn sách BOOK001 (Có sẵn). 3. Mượn cho thành viên MEM004 (Lê Cần Cù, Tạm ngưng). | TV: MEM004 (Tạm ngưng), Sách: BOOK001 | Không cho mượn. Hiển thị thông báo lỗi chứa "tạm ngưng" — KHÔNG hiển thị "hết hạn" (theo SRS: phải mô tả đúng lý do). | REQ-04 | EP, DT-R3 |
| TC-16 | Thành viên Hết hạn mượn sách — Từ chối (DT-R5) | Đã đăng nhập bằng tài khoản Thủ thư. Dữ liệu đã reset. | 1. Chuyển đến tab "Sách". 2. Chọn sách BOOK001 (Có sẵn). 3. Mượn cho thành viên MEM005 (Phạm Trung Bình, Hết hạn). | TV: MEM005 (Hết hạn), Sách: BOOK001 | Không cho mượn. Hiển thị thông báo lỗi chứa "hết hạn" — KHÔNG hiển thị "tạm ngưng" (theo SRS: phải mô tả đúng lý do). | REQ-04 | EP, DT-R5 |
| TC-17 | Mượn sách — Giới hạn tối đa 3 sách (BVA boundary, DT-R2) | Đã đăng nhập bằng MEM003 (`dam.tran@email.com`, Hoạt động, đang mượn 0 sách). Dữ liệu đã reset. | 1. Mượn BOOK001 → ghi nhận (đang mượn 1). 2. Mượn BOOK002 → ghi nhận (đang mượn 2). 3. Mượn BOOK004 → ghi nhận (đang mượn 3). 4. Mượn BOOK005 → ghi nhận (đang mượn 3, thử mượn cuốn thứ 4). | TV: MEM003, BVA boundary: 2 sách → 3 sách | Bước 1–3: Mượn thành công (số sách < 3 hoặc = 3 sau khi mượn). Bước 4: Từ chối, hiển thị thông báo lỗi "Đã đạt giới hạn mượn tối đa (3 sách)". | REQ-04 | BVA, DT-R2 |

### REQ-05: Trả sách

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-18 | Trả sách đúng hạn — Trạng thái cập nhật | Đã đăng nhập bằng MEM006 (`biet.hoang@email.com`). Dữ liệu đã reset (MEM006 đang mượn BOOK003...). Ghi chú: thực tế MEM006 đang mượn BOOK013 (BR003). | 1. Chuyển đến tab "Mượn / Trả". 2. Chọn phiếu mượn BR003 (MEM006, BOOK013). 3. Nhấn nút **Trả**. | Phiếu: BR003, Sách: BOOK013 | Trả thành công. BOOK013 chuyển trạng thái "Có sẵn". Phiếu BR003 chuyển trạng thái "Đã trả". Không hiển thị cảnh báo quá hạn (BR003 còn hạn). | REQ-05 | EP |
| TC-19 | Trả sách quá hạn — Hiển thị cảnh báo | Đã đăng nhập bằng MEM002 (`ba.nguyen@email.com`). Dữ liệu đã reset. Thủ thư đã nhấn "Kiểm tra quá hạn" (BR001 được đánh dấu quá hạn). | 1. Chuyển đến tab "Mượn / Trả". 2. Chọn phiếu mượn BR001 (MEM002, BOOK003, quá hạn). 3. Nhấn nút **Trả**. | Phiếu: BR001, Sách: BOOK003 | Hiển thị cảnh báo quá hạn rõ ràng trước khi trả. Sau khi xác nhận: trả thành công, BOOK003 chuyển trạng thái "Có sẵn", phiếu BR001 chuyển trạng thái "Đã trả". | REQ-05 | EP |

### REQ-06: Xử lý sách quá hạn

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-20 | Thủ thư kiểm tra quá hạn — Đánh dấu phiếu quá hạn | Đã đăng nhập bằng Thủ thư (`librarian@library.com`). Dữ liệu đã reset (BR001 có dueDate 15/09/2024 ≤ hôm nay). | 1. Chuyển đến tab "Mượn / Trả". 2. Nhấn nút **Kiểm tra quá hạn**. 3. Kiểm tra trạng thái phiếu BR001. | N/A | BR001 (dueDate 15/09/2024 ≤ ngày hiện tại) được đánh dấu trạng thái "Quá hạn". Các phiếu khác có dueDate > hôm nay không bị đánh dấu. | REQ-06 | EP |
| TC-21 | Thành viên xem phiếu quá hạn của mình | Đã đăng nhập bằng MEM002 (`ba.nguyen@email.com`). Thủ thư đã nhấn "Kiểm tra quá hạn". | 1. Chuyển đến tab "Mượn / Trả". 2. Xem danh sách phiếu mượn của tôi. | N/A | MEM002 thấy phiếu BR001 (BOOK003) với trạng thái "Quá hạn". Không thấy phiếu của thành viên khác. | REQ-06 | EP |

### REQ-07: Quản lý thành viên

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-22 | Thêm thành viên mới hợp lệ | Đã đăng nhập bằng Thủ thư (`librarian@library.com`). Dữ liệu đã reset. | 1. Chuyển đến tab "Thành viên". 2. Nhấn nút **Thêm thành viên**. 3. Nhập họ tên `Nguyễn Văn Mới`. 4. Nhập email `nguyenvanmoi@email.com`. 5. Nhập SĐT `0901234567`. 6. Nhấn nút **Tạo** / **Lưu**. | Họ tên: `Nguyễn Văn Mới`, Email: `nguyenvanmoi@email.com`, SĐT: `0901234567` | Tạo thành công. Thành viên mới xuất hiện trong danh sách với trạng thái "Hoạt động". | REQ-07 | EP |
| TC-23 | Thêm thành viên — Email không hợp lệ, thiếu dấu chấm trong domain (BVA) | Đã đăng nhập bằng Thủ thư. Dữ liệu đã reset. | 1. Chuyển đến tab "Thành viên". 2. Nhấn nút **Thêm thành viên**. 3. Nhập họ tên `Test User`. 4. Nhập email `user@domain` (không có dấu `.` trong domain). 5. Nhập SĐT `0901234567`. 6. Nhấn nút **Tạo** / **Lưu**. | Email: `user@domain` (BVA: biên hợp lệ/không hợp lệ) | Không tạo. Hiển thị thông báo lỗi email không hợp lệ. Theo SRS REQ-07: email phải có `@` VÀ dấu `.` trong domain. | REQ-07 | BVA |
| TC-24 | Thêm thành viên — Email đã tồn tại | Đã đăng nhập bằng Thủ thư. Dữ liệu đã reset. | 1. Chuyển đến tab "Thành viên". 2. Nhấn nút **Thêm thành viên**. 3. Nhập họ tên `Trùng Email`. 4. Nhập email `ba.nguyen@email.com` (đã tồn tại). 5. Nhập SĐT `0901234567`. 6. Nhấn nút **Tạo** / **Lưu**. | Email: `ba.nguyen@email.com` (đã tồn tại) | Không tạo. Hiển thị thông báo lỗi email đã tồn tại. | REQ-07 | EP |

### REQ-08: Tra cứu phiếu mượn

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-25 | Thủ thư xem tất cả phiếu mượn | Đã đăng nhập bằng Thủ thư (`librarian@library.com`). Dữ liệu đã reset. | 1. Chuyển đến tab "Mượn / Trả". 2. Tra cứu phiếu mượn. | N/A | Hiển thị tất cả 5 phiếu mượn: BR001–BR005. Mỗi phiếu hiển thị: mã phiếu, sách mượn, ngày mượn, hạn trả, trạng thái. | REQ-08 | EP |
| TC-26 | Thành viên chỉ xem phiếu mượn của mình | Đã đăng nhập bằng MEM002 (`ba.nguyen@email.com`). Dữ liệu đã reset. | 1. Chuyển đến tab "Mượn / Trả". 2. Xem "Phiếu mượn của tôi". | N/A | Hiển thị chỉ phiếu mượn của MEM002: BR001 (BOOK003, Đang mượn) và BR004 (BOOK005, Đã trả). Không hiển thị phiếu của thành viên khác. | REQ-08 | EP |
| TC-27 | Thành viên không xem được phiếu mượn của người khác | Đã đăng nhập bằng MEM002 (`ba.nguyen@email.com`). Dữ liệu đã reset. | 1. Chuyển đến tab "Mượn / Trả". 2. Thử tra cứu phiếu mượn theo mã thành viên MEM003 hoặc MEM006. | Mã thành viên: MEM003 hoặc MEM006 | Không hiển thị phiếu mượn của MEM003/MEM006. Theo SRS REQ-08: thành viên KHÔNG được xem phiếu mượn của thành viên khác. | REQ-08 | EP |

### REQ-07 (bổ sung): Kỹ thuật EP — Lọc theo thể loại không phân biệt hoa/thường

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-28 | Lọc sách theo thể loại — Không phân biệt hoa/thường | Đã đăng nhập, đang ở tab "Sách" | 1. Nhập hoặc chọn thể loại `"công nghệ"` (chữ thường) trong bộ lọc. | Thể loại: `"công nghệ"` (chữ thường) | Hiển thị kết quả giống lọc "Công nghệ": 8 sách thể loại Công nghệ (BOOK001, 002, 003, 005, 008, 009, 010, 011). Lọc phải KHÔNG phân biệt hoa/thường theo SRS REQ-03. | REQ-03 | BVA |

---

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|----------------|-------|---------|----------------------|
| Đăng nhập | 4 | REQ-01 | EP (3 phân vùng: email đúng/sai/rỗng, MK đúng/sai/rỗng) |
| Xem danh sách sách | 2 | REQ-02 | EP (vai trò Thủ thư/Thành viên, real-time update) |
| Tìm kiếm & Lọc | 5 | REQ-03 | EP (có/không kết quả), BVA (hoa/thường boundary) |
| Mượn sách | 6 | REQ-04 | EP (trạng thái sách/TV), BVA (giới hạn 3 sách), Decision Table (R1–R13) |
| Trả sách | 2 | REQ-05 | EP (đúng hạn/quá hạn) |
| Xử lý quá hạn | 2 | REQ-06 | EP (có/không quá hạn, vai trò kiểm tra) |
| Quản lý thành viên | 3 | REQ-07 | EP (email hợp lệ/trùng), BVA (email biên hợp lệ/không hợp lệ) |
| Tra cứu phiếu mượn | 3 | REQ-08 | EP (Thủ thư xem tất cả/Thành viên chỉ xem mình) |
| Lọc không phân biệt hoa/thường | 1 | REQ-03 | BVA (chữ thường boundary) |
| **Tổng** | **28** | **REQ-01 → REQ-08** | **EP + BVA + Decision Table** |
