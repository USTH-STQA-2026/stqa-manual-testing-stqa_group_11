# Test Execution — Kết quả thực thi kiểm thử

> **Hướng dẫn**: Chạy từng TC trên hệ thống https://stqa.rbc.vn, ghi lại kết quả thực tế.
> Kết luận: **Pass** (kết quả đúng), **Fail** (kết quả sai → tạo bug report), **Blocked** (không thực hiện được vì lỗi khác chặn), **Not Run** (chưa chạy).

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 11 |
| **Ngày thực thi** | 06/06/2026 |
| **Trình duyệt** | Chrome (bản mới nhất) |
| **Hệ điều hành** | Windows 11 |

---

## Kết quả chi tiết

| Mã TC | Nhóm chức năng | Kết quả mong đợi (tóm tắt) | Kết quả thực tế | Kết luận | Bug |
|-------|---------------|---------------------------|-----------------|---------|----|
| TC-01 | Đăng nhập | Đăng nhập thành công, AppBar hiển thị tên + vai trò Thủ thư | Chuyển sang trang chủ. AppBar hiển thị "Nguyễn Thủ Thư — Thủ thư" | Pass | |
| TC-02 | Đăng nhập | Thông báo "Không tìm thấy thành viên" | Hiển thị thông báo "Không tìm thấy thành viên" | Pass | |
| TC-03 | Đăng nhập | Thông báo "Mật khẩu không đúng" | Hiển thị thông báo "Mật khẩu không đúng" | Pass | |
| TC-04 | Đăng nhập | Thông báo "Vui lòng nhập email và mật khẩu" | Hiển thị thông báo "Vui lòng nhập email và mật khẩu" | Pass | |
| TC-05 | Xem sách | Cả 2 vai trò xem được 20 sách với đủ thông tin | Cả 2 vai trò xem đủ 20 sách, mỗi sách hiển thị đầy đủ: tên, tác giả, thể loại, năm XB, trạng thái | Pass | |
| TC-06 | Xem sách | BOOK001 chuyển "Đang mượn" sau khi mượn, real-time | Hiển thị trạng thái "Đang mượn" ngay sau khi mượn, không cần F5 | Pass | |
| TC-07 | Tìm kiếm | Hiển thị BOOK001 khi tìm "Flutter" | Hiển thị BOOK001 "Lập trình Flutter cơ bản" | Pass | |
| TC-08 | Tìm kiếm | Hiển thị BOOK001 + BOOK009 khi tìm "Nguyễn Minh Đức" | Hiển thị BOOK001 + BOOK009 (2 sách của tác giả Nguyễn Minh Đức) | Pass | |
| TC-09 | Tìm kiếm | Danh sách rỗng, thông báo "Không tìm thấy sách" | Hiện thông báo "Không tìm thấy sách" khi nhập từ khóa không tồn tại | Pass | |
| TC-10 | Tìm kiếm | "flutter" và "FLUTTER" cho kết quả giống "Flutter" | Đều hiển thị kết quả giống nhau — case-insensitive hoạt động đúng | Pass | |
| TC-11 | Lọc sách | Hiển thị 8 sách thể loại Công nghệ | Hiển thị đủ **8** sách thể loại Công nghệ | Pass | |
| TC-12 | Mượn sách | Mượn thành công BOOK004, tạo phiếu mới, due date +14 ngày | Mượn thành công, tạo phiếu mới, due date = borrow date + 14 ngày (năm 2026 đúng) | Pass | |
| TC-13 | Mượn sách | Từ chối mượn BOOK003 (đang mượn), không có nút mượn | Không có nút mượn trên sách "Đang mượn" | Pass | |
| TC-14 | Mượn sách | Từ chối mượn BOOK007 (thất lạc), không có nút mượn | Không có nút mượn trên sách "Thất lạc" | Pass | |
| TC-15 | Mượn sách | Từ chối MEM004 (tạm ngưng), thông báo chứa "tạm ngưng" | Từ chối mượn nhưng thông báo hiển thị **"Thành viên đã hết hạn. Không thể mượn sách."** thay vì "tạm ngưng" | **Fail** | BUG-02 |
| TC-16 | Mượn sách | Từ chối MEM005 (hết hạn), thông báo chứa "hết hạn" | Từ chối mượn, thông báo hiển thị **"Thành viên đã hết hạn. Không thể mượn sách."** — đúng theo SRS | Pass | |
| TC-17 | Mượn sách | Bước 1-3: mượn thành công; Bước 4: từ chối, giới hạn 3 sách | Bước 1-3: mượn thành công. Bước 4: mượn sách thứ 4 **vẫn thành công**, không bị từ chối — thành viên đang mượn 4 sách | **Fail** | BUG-01 |
| TC-18 | Trả sách | Trả BOOK013 thành công, sách → "Có sẵn", không cảnh báo | Trả thành công, BOOK013 → "Có sẵn", không có cảnh báo quá hạn (sách không quá hạn) | Pass | |
| TC-19 | Trả sách | Cảnh báo quá hạn khi trả BOOK003 (BR001) | Trả thành công, BOOK003 → "Có sẵn". **Không hiển thị cảnh báo quá hạn** | **Fail** | — *(unimplemented)* |
| TC-20 | Quá hạn | BR001 + BR003 đánh dấu "Quá hạn" sau khi Thủ thư nhấn kiểm tra | BR001 và BR003 đều được đánh dấu "Quá hạn" (đúng: cả 2 có dueDate ≤ hôm nay) | Pass | |
| TC-21 | Quá hạn | MEM002 thấy BR001 quá hạn, không thấy phiếu người khác | MEM002 thấy BR001 "Quá hạn", tab mặc định chỉ hiển thị phiếu của chính mình | Pass | |
| TC-22 | Thành viên | Tạo thành viên mới thành công (email hợp lệ) | Email hợp lệ `nguyenvanmoi@email.com` bị từ chối với thông báo **"Email không hợp lệ"** | **Fail** | BUG-03 |
| TC-23 | Thành viên | Lỗi email không hợp lệ (`user@domain` thiếu dấu chấm domain) | Email không hợp lệ `user@domain` (thiếu dấu `.` trong domain) **được chấp nhận**, tạo thành công | **Fail** | BUG-04 |
| TC-24 | Thành viên | Lỗi email đã tồn tại, thông báo "Email đã tồn tại" | Từ chối tạo nhưng thông báo sai **"Email không hợp lệ"** thay vì "Email đã tồn tại" | **Fail** | BUG-05 |
| TC-25 | Phiếu mượn | Thủ thư xem tất cả 5 phiếu BR001–BR005 | Hiển thị đủ 5 phiếu: BR001, BR003 "Đang mượn"; BR002, BR004, BR005 "Đã trả" | Pass | |
| TC-26 | Phiếu mượn | MEM002 chỉ thấy BR001 + BR004 (phiếu của mình) | Chỉ thấy BR001 và BR004, không thấy phiếu thành viên khác trong tab mặc định | Pass | |
| TC-27 | Phiếu mượn | MEM002 không xem được phiếu của MEM003/MEM006 | MEM002 **xem được** phiếu của MEM003 (BR002, BR005) và MEM006 (BR003) khi nhập mã thành viên — vi phạm REQ-08 | **Fail** | BUG-06 |
| TC-28 | Lọc sách | "công nghệ" (chữ thường) cho kết quả giống "Công nghệ" | Lọc "công nghệ" (chữ thường) trả về **"Không tìm thấy sách nào"** — 0 kết quả. Bộ lọc phân biệt hoa/thường | **Fail** | BUG-07 |

---

## Ghi chú quan trọng

### Ghi chú 1: TC-15/TC-16 — Phương pháp thực thi điều chỉnh

SRS yêu cầu Thủ thư có quyền "mượn sách cho thành viên" (SRS Section 1). Tuy nhiên, hệ thống **không hiển thị nút Mượn** khi đăng nhập bằng Thủ thư — tính năng này chưa được triển khai (unimplemented feature). Do đó, TC-15 và TC-16 được thực thi bằng cách **đăng nhập trực tiếp bằng tài khoản thành viên** (MEM004/MEM005) thay vì qua Thủ thư.

Cách thực thi này vẫn hợp lệ vì:
- SRS REQ-01 chỉ kiểm tra email + mật khẩu cho đăng nhập — không yêu cầu kiểm tra trạng thái thành viên ở bước đăng nhập.
- SRS REQ-04 quy định từ chối mượn sách áp dụng tại **bước mượn** — bất kể ai thực hiện thao tác (thành viên tự mượn hoặc Thủ thư mượn hộ).

### Ghi chú 2: TC-19 — Tính năng chưa triển khai (unimplemented feature)

SRS REQ-05 yêu cầu hiển thị cảnh báo quá hạn khi trả sách quá hạn, nhưng hệ thống không hiển thị bất kỳ cảnh báo nào. Xác nhận thêm: BR001 **được đánh dấu "Quá hạn"** đúng, nhưng khi nhấn **Trả** → không có dialog/warning nào xuất hiện, trả sách hoàn tất ngay lập tức.

Đây là **tính năng chưa được triển khai** (unimplemented feature), không phải lỗi code (bug). TC-19 ghi nhận Fail vì kết quả thực tế không khớp SRS, nhưng **không tạo bug report**.

### Ghi chú 3: Phát hiện bổ sung — tính năng chưa triển khai

Khi đăng nhập bằng Thủ thư, tab "Sách" **không hiển thị nút Mượn** trên bất kỳ sách nào. Thủ thư cũng **không thể tạo phiếu mượn** cho thành viên từ tab "Mượn / Trả". Theo SRS Section 1: Thủ thư phải có quyền "mượn/trả sách cho thành viên" → Đây là **tính năng chưa triển khai** (unimplemented feature), không phải bug code.

---

## Tổng hợp kết quả

| Chỉ số | Giá trị |
|--------|---------|
| Tổng số test case | 28 |
| Pass | 20 |
| Fail (bug) | 7 |
| Fail (unimplemented feature — không tạo bug report) | 1 (TC-19) |
| Blocked | 0 |
| Not Run | 0 |
| **Tỷ lệ Pass** | **71.4%** |
| **Số bug phát hiện** | **7** |

### Kết quả theo nhóm chức năng

| Nhóm | Tổng TC | Pass | Fail | Tỷ lệ Pass |
|------|---------|------|------|------------|
| Đăng nhập (REQ-01) | 4 | 4 | 0 | 100% |
| Xem sách (REQ-02) | 2 | 2 | 0 | 100% |
| Tìm kiếm & Lọc (REQ-03) | 6 | 5 | 1 | 83.3% |
| Mượn sách (REQ-04) | 6 | 4 | 2 | 66.7% |
| Trả sách (REQ-05) | 2 | 1 | 1* | 50% |
| Quá hạn (REQ-06) | 2 | 2 | 0 | 100% |
| Thành viên (REQ-07) | 3 | 0 | 3 | 0% |
| Phiếu mượn (REQ-08) | 3 | 2 | 1 | 66.7% |

> *\* TC-19 Fail do tính năng chưa triển khai (unimplemented), không phải bug code.*
