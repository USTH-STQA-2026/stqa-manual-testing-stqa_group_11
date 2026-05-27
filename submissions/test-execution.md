# Test Execution — Kết quả thực thi kiểm thử

> **Hướng dẫn**: Chạy từng TC trên hệ thống https://stqa.rbc.vn, ghi lại kết quả thực tế.
> Kết luận: **Pass** (kết quả đúng), **Fail** (kết quả sai → tạo bug report), **Blocked** (không thực hiện được vì lỗi khác chặn), **Not Run** (chưa chạy).

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 11 |
| **Ngày thực thi** | `<!-- DD/MM/YYYY -->` |
| **Trình duyệt** | Chrome `<!-- version -->` |
| **Hệ điều hành** | `<!-- Windows / macOS / Linux -->` |

---

## Kết quả chi tiết

| Mã TC | Nhóm chức năng | Kết quả mong đợi (tóm tắt) | Kết quả thực tế | Kết luận | Minh chứng | Bug |
|-------|---------------|---------------------------|-----------------|---------|-----------|----|
| TC-01 | Đăng nhập | Đăng nhập thành công, AppBar hiển thị tên + vai trò Thủ thư | | | | |
| TC-02 | Đăng nhập | Thông báo "Không tìm thấy thành viên" | | | | |
| TC-03 | Đăng nhập | Thông báo "Mật khẩu không đúng" | | | | |
| TC-04 | Đăng nhập | Thông báo "Vui lòng nhập email và mật khẩu" | | | | |
| TC-05 | Xem sách | Cả 2 vai trò xem được 20 sách với đủ thông tin | | | | |
| TC-06 | Xem sách | BOOK001 chuyển "Đang mượn" sau khi mượn, real-time | | | | |
| TC-07 | Tìm kiếm | Hiển thị BOOK001 khi tìm "Flutter" | | | | |
| TC-08 | Tìm kiếm | Hiển thị BOOK001 + BOOK009 khi tìm "Nguyễn Minh Đức" | | | | |
| TC-09 | Tìm kiếm | Danh sách rỗng, thông báo "Không tìm thấy sách" | | | | |
| TC-10 | Tìm kiếm | "flutter" và "FLUTTER" cho kết quả giống "Flutter" | | | | |
| TC-11 | Lọc sách | Hiển thị 8 sách thể loại Công nghệ | | | | |
| TC-12 | Mượn sách | Mượn thành công BOOK004 cho MEM002, tạo phiếu mới | | | | |
| TC-13 | Mượn sách | Từ chối mượn BOOK003 (đang mượn), thông báo sách không có sẵn | | | | |
| TC-14 | Mượn sách | Từ chối mượn BOOK007 (thất lạc), thông báo sách không có sẵn | | | | |
| TC-15 | Mượn sách | Từ chối MEM004 (tạm ngưng), thông báo chứa "tạm ngưng" | | | | |
| TC-16 | Mượn sách | Từ chối MEM005 (hết hạn), thông báo chứa "hết hạn" | | | | |
| TC-17 | Mượn sách | Bước 1-3: mượn thành công; Bước 4: từ chối, giới hạn 3 sách | | | | |
| TC-18 | Trả sách | Trả BOOK013 thành công, sách → "Có sẵn", không cảnh báo | | | | |
| TC-19 | Trả sách | Cảnh báo quá hạn khi trả BOOK003 (BR001) | | | | |
| TC-20 | Quá hạn | BR001 đánh dấu "Quá hạn" sau khi Thủ thư nhấn kiểm tra | | | | |
| TC-21 | Quá hạn | MEM002 thấy BR001 quá hạn, không thấy phiếu người khác | | | | |
| TC-22 | Thành viên | Tạo thành viên mới thành công | | | | |
| TC-23 | Thành viên | Lỗi email không hợp lệ (`user@domain` thiếu dấu chấm) | | | | |
| TC-24 | Thành viên | Lỗi email đã tồn tại | | | | |
| TC-25 | Phiếu mượn | Thủ thư xem tất cả 5 phiếu BR001–BR005 | | | | |
| TC-26 | Phiếu mượn | MEM002 chỉ thấy BR001 + BR004 | | | | |
| TC-27 | Phiếu mượn | MEM002 không xem được phiếu của MEM003/MEM006 | | | | |
| TC-28 | Lọc sách | "công nghệ" (chữ thường) cho kết quả giống "Công nghệ" | | | | |

---

## Tổng hợp kết quả

| Chỉ số | Giá trị |
|--------|---------|
| Tổng số test case | 28 |
| Pass | `<!-- số -->` |
| Fail | `<!-- số -->` |
| Blocked | `<!-- số -->` |
| Not Run | `<!-- số -->` |
| **Tỷ lệ Pass** | `<!-- xx% -->` |

### Kết quả theo nhóm chức năng

| Nhóm | Tổng TC | Pass | Fail | Tỷ lệ Pass |
|------|---------|------|------|------------|
| Đăng nhập (REQ-01) | 4 | | | |
| Xem sách (REQ-02) | 2 | | | |
| Tìm kiếm & Lọc (REQ-03) | 6 | | | |
| Mượn sách (REQ-04) | 6 | | | | 
| Trả sách (REQ-05) | 2 | | | |
| Quá hạn (REQ-06) | 2 | | | |
| Thành viên (REQ-07) | 3 | | | |
| Phiếu mượn (REQ-08) | 3 | | | |
