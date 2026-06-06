# Bug Reports — Báo cáo lỗi

> **Hướng dẫn**: Tạo 1 mục bug cho mỗi TC có kết quả **Fail** (do lỗi code, không phải tính năng chưa triển khai).
> Xem [examples/sample-bug-report.md](../examples/sample-bug-report.md) để hiểu cách viết bug report tốt.
> Mỗi bug cần: tiêu đề mô tả hành vi lỗi, bước tái hiện, expected vs actual, severity + giải thích.

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 11 |
| **Ngày báo cáo** | 06/06/2026 |

> **📌 Lưu ý**: TC-19 (không hiển thị cảnh báo quá hạn khi trả sách) và việc "Thủ thư không có nút Mượn" là **tính năng chưa triển khai** (unimplemented features), không phải lỗi code. Do đó, không tạo bug report cho các trường hợp này.

---

## BUG-01

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-01 |
| **TC liên quan** | TC-17 |
| **REQ liên quan** | REQ-04 |
| **Mức độ** | High |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Mượn được sách thứ 4 — vượt giới hạn tối đa 3 sách/thành viên (lỗi off-by-one: `>` thay vì `>=`)

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng tài khoản thành viên đang hoạt động với 0 sách đang mượn. Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Nhập email `dam.tran@email.com` (MEM003, Hoạt động, 0 sách đang mượn) và mật khẩu `password123`.
2. Nhấn nút **Đăng nhập**.
3. Chuyển đến tab **Sách**.
4. Mượn sách BOOK001 → thành công (đang mượn 1 sách).
5. Mượn sách BOOK002 → thành công (đang mượn 2 sách).
6. Mượn sách BOOK004 → thành công (đang mượn 3 sách).
7. Mượn sách BOOK005 (thử mượn cuốn thứ 4).

**Kết quả mong đợi:**
Bước 4–6: Mượn thành công. Bước 7: Hệ thống **từ chối** mượn cuốn thứ 4. Hiển thị thông báo lỗi "Đã đạt giới hạn mượn tối đa (3 sách)". (Theo SRS REQ-04: tối đa 3 sách/thành viên cùng lúc).

**Kết quả thực tế:**
Bước 4–6: Mượn thành công. Bước 7: Hệ thống **cho phép mượn cuốn thứ 4** — không bị từ chối, không hiển thị thông báo lỗi. Thành viên MEM003 đang mượn 4 sách cùng lúc.

**Tác động:**
Vi phạm nghiêm trọng quy tắc nghiệp vụ cốt lõi (BR-01): giới hạn tối đa 3 sách/thành viên. Cho phép thành viên mượn không giới hạn, gây mất kiểm soát số lượng sách mỗi người. Nguyên nhân khả thi: code sử dụng toán tử `>` thay vì `>=` khi so sánh số sách đang mượn với giới hạn (off-by-one error).

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị MEM003 đang mượn 4 sách)*

**Đề xuất xử lý:**
Sửa điều kiện kiểm tra giới hạn mượn từ `if (currentBorrowCount > maxBooksPerMember)` sang `if (currentBorrowCount >= maxBooksPerMember)`. Khi số sách đang mượn = 3 (giới hạn tối đa), phải từ chối mượn thêm.

---

## BUG-02

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-02 |
| **TC liên quan** | TC-15 |
| **REQ liên quan** | REQ-04 |
| **Mức độ** | Medium |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Thành viên "Tạm ngưng" mượn sách nhận thông báo lỗi "hết hạn" thay vì "tạm ngưng"

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Trang đăng nhập đã mở, dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Nhập email `cu.le@email.com` (MEM004, trạng thái Tạm ngưng) và mật khẩu `password123`.
2. Nhấn nút **Đăng nhập**.
3. Chuyển đến tab **Sách**.
4. Tìm sách BOOK001 (Lập trình Flutter cơ bản, trạng thái "Có sẵn").
5. Nhấn nút **Mượn** trên BOOK001.

**Kết quả mong đợi:**
Hệ thống từ chối mượn sách. Hiển thị thông báo lỗi chứa từ **"tạm ngưng"** — ví dụ: "Thành viên đang bị tạm ngưng. Không thể mượn sách." (Theo SRS REQ-04: thông báo lỗi phải mô tả **đúng lý do** từ chối, tạm ngưng ≠ hết hạn).

**Kết quả thực tế:**
Hệ thống từ chối mượn sách nhưng hiển thị thông báo **"Thành viên đã hết hạn. Không thể mượn sách."** thay vì "tạm ngưng". Người dùng không thể phân biệt được lý do thực tế bị từ chối là do tạm ngưng hay hết hạn.

**Tác động:**
Thành viên bị tạm ngưng nhận thông báo sai, gây nhầm lẫn về lý do từ chối. Vi phạm quy tắc SRS REQ-04 yêu cầu thông báo lỗi phải mô tả đúng lý do. Thủ thư và thành viên không xác định được trạng thái tài khoản thực tế để có hành động khắc phục phù hợp.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị thông báo lỗi "hết hạn" khi MEM004 mượn sách)*

**Đề xuất xử lý:**
Kiểm tra lại logic xử lý thông báo lỗi trong hàm mượn sách. Đảm bảo khi trạng thái thành viên là "Tạm ngưng" (suspended), thông báo lỗi phải chứa từ "tạm ngưng". Có thể code đang gộp chung 2 trường hợp "Tạm ngưng" và "Hết hạn" vào cùng 1 nhánh thông báo.

---

## BUG-03

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-03 |
| **TC liên quan** | TC-22 |
| **REQ liên quan** | REQ-07 |
| **Mức độ** | High |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Email hợp lệ bị từ chối với thông báo "Email không hợp lệ" khi thêm thành viên mới

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng Thủ thư (`librarian@library.com` / `admin123`). Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Chuyển đến tab **Thành viên**.
2. Nhấn nút **Thêm thành viên**.
3. Nhập họ tên: `Nguyễn Văn Mới`.
4. Nhập email: `nguyenvanmoi@email.com` (email hợp lệ — có `@` VÀ có dấu `.` trong domain).
5. Nhập số điện thoại: `0901234567`.
6. Nhấn nút **Tạo** / **Lưu**.

**Kết quả mong đợi:**
Tạo thành viên mới thành công. Thành viên xuất hiện trong danh sách với trạng thái "Hoạt động". (Theo SRS REQ-07: email hợp lệ phải có `@` VÀ dấu `.` trong domain — `nguyenvanmoi@email.com` thỏa mãn cả 2 điều kiện).

**Kết quả thực tế:**
Hệ thống từ chối tạo thành viên mới. Hiển thị thông báo lỗi "Email không hợp lệ" dù email `nguyenvanmoi@email.com` hoàn toàn hợp lệ theo SRS.

**Tác động:**
Không thể thêm bất kỳ thành viên mới nào có email hợp lệ — **chức năng quản lý thành viên hoàn toàn không sử dụng được**. Đây là lỗi nghiêm trọng làm tê liệt toàn bộ tính năng REQ-07. Kết hợp với BUG-04, logic xác thực email bị **đảo ngược hoàn toàn**: hợp lệ → từ chối, không hợp lệ → chấp nhận. Nguyên nhân khả thi: logic xác thực email bị đảo ngược hoặc sai biểu thức chính quy (regex).

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị thông báo "Email không hợp lệ" với email hợp lệ)*

**Đề xuất xử lý:**
Kiểm tra lại logic xác thực email trong form thêm thành viên. Biểu thức xác thực có thể bị sai hoặc bị đảo ngược — chấp nhận email không hợp lệ (thiếu dấu chấm domain) nhưng từ chối email hợp lệ. Sửa regex/logic kiểm tra email để đảm bảo: email có `@` VÀ có `.` trong phần domain thì hợp lệ.

---

## BUG-04

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-04 |
| **TC liên quan** | TC-23 |
| **REQ liên quan** | REQ-07 |
| **Mức độ** | High |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Email không hợp lệ `user@domain` (thiếu dấu chấm trong domain) được chấp nhận khi thêm thành viên

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng Thủ thư. Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Chuyển đến tab **Thành viên**.
2. Nhấn nút **Thêm thành viên**.
3. Nhập họ tên: `Test User`.
4. Nhập email: `user@domain` (có `@` nhưng **không có dấu `.`** trong phần domain).
5. Nhập số điện thoại: `0901234567`.
6. Nhấn nút **Tạo** / **Lưu**.

**Kết quả mong đợi:**
Hệ thống **từ chối** tạo thành viên. Hiển thị thông báo lỗi email không hợp lệ. (Theo SRS REQ-07: email phải hợp lệ — có `@` **VÀ** có dấu `.` trong phần domain. Email `user@domain` KHÔNG hợp lệ vì thiếu dấu `.` trong domain).

**Kết quả thực tế:**
Hệ thống **chấp nhận** email `user@domain`, tạo thành viên mới thành công. Không hiển thị thông báo lỗi.

**Tác động:**
Cho phép tạo thành viên với email không hợp lệ, vi phạm quy tắc xác thực SRS REQ-07 và BR-08. Dữ liệu thành viên bị sai — email không hợp lệ có thể gây lỗi khi cần gửi thông báo hoặc liên lạc. Kết hợp với BUG-03, logic xác thực email bị **đảo ngược hoàn toàn**: hợp lệ → từ chối, không hợp lệ → chấp nhận.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị tạo thành viên thành công với email `user@domain`)*

**Đề xuất xử lý:**
Sửa logic xác thực email — đảo ngược điều kiện kiểm tra. Đảm bảo email `user@domain` (thiếu dấu `.` trong domain) bị từ chối, và email `nguyenvanmoi@email.com` (có đủ `@` và `.` trong domain) được chấp nhận. Có thể cần viết lại biểu thức regex xác thực email hoàn toàn.

---

## BUG-05

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-05 |
| **TC liên quan** | TC-24 |
| **REQ liên quan** | REQ-07 |
| **Mức độ** | Medium |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Email đã tồn tại hiển thị thông báo sai "Email không hợp lệ" thay vì "Email đã tồn tại"

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng Thủ thư. Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Chuyển đến tab **Thành viên**.
2. Nhấn nút **Thêm thành viên**.
3. Nhập họ tên: `Trùng Email`.
4. Nhập email: `ba.nguyen@email.com` (email đã tồn tại trong hệ thống — MEM002).
5. Nhập số điện thoại: `0901234567`.
6. Nhấn nút **Tạo** / **Lưu**.

**Kết quả mong đợi:**
Hệ thống từ chối tạo thành viên. Hiển thị thông báo lỗi **"Email đã tồn tại"** (hoặc tương tự, mô tả đúng lý do từ chối là do trùng email). (Theo SRS REQ-07: "Không cho phép tạo email đã tồn tại → thông báo lỗi").

**Kết quả thực tế:**
Hệ thống từ chối tạo thành viên nhưng hiển thị thông báo **"Email không hợp lệ"** thay vì "Email đã tồn tại". Lý do từ chối đúng (email trùng) nhưng thông báo sai (hiển thị lý do không hợp lệ).

**Tác động:**
Người dùng (Thủ thư) nhận thông báo sai — không biết lý do thực tế là email đã tồn tại, có thể hiểu nhầm rằng định dạng email sai và thử nhập lại email khác thay vì hiểu rằng cần dùng email mới. Gây nhầm lẫn và mất thời gian.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị thông báo "Email không hợp lệ" khi nhập email đã tồn tại)*

**Đề xuất xử lý:**
Tách riêng 2 điều kiện kiểm tra: (1) Kiểm tra định dạng email hợp lệ → thông báo "Email không hợp lệ", (2) Kiểm tra email đã tồn tại → thông báo "Email đã tồn tại". Đảm bảo kiểm tra trùng email được thực hiện và trả về thông báo phù hợp trước khi kiểm tra định dạng.

---

## BUG-06

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-06 |
| **TC liên quan** | TC-27 |
| **REQ liên quan** | REQ-08 |
| **Mức độ** | High |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Thành viên xem được phiếu mượn của thành viên khác — vi phạm quyền truy cập SRS REQ-08

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng tài khoản thành viên MEM002. Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Nhập email `ba.nguyen@email.com` (MEM002) và mật khẩu `password123`.
2. Nhấn nút **Đăng nhập**.
3. Chuyển đến tab **Mượn / Trả**.
4. Tab mặc định chỉ hiển thị phiếu của MEM002 (BR001, BR004) — đúng.
5. Trong phần tra cứu phiếu mượn, nhập mã thành viên **MEM003**.
6. Quan sát kết quả — thấy phiếu BR002 và BR005 của MEM003.
7. Nhập mã thành viên **MEM006**.
8. Quan sát kết quả — thấy phiếu BR003 của MEM006.

**Kết quả mong đợi:**
MEM002 **không xem được** phiếu mượn của MEM003 hoặc MEM006. Theo SRS REQ-08: "Thành viên chỉ xem phiếu mượn **của chính mình**. **KHÔNG được xem phiếu mượn của thành viên khác.**"

**Kết quả thực tế:**
Tab mặc định hiển thị đúng (chỉ phiếu của MEM002). Tuy nhiên, khi nhập mã thành viên khác (MEM003/MEM006), MEM002 có thể xem đầy đủ phiếu mượn của họ. Không có bất kỳ hạn chế quyền truy cập nào cho chức năng tra cứu.

**Tác động:**
Vi phạm nghiêm trọng quy tắc bảo mật phiếu mượn (BR-07 và SRS REQ-08). Thành viên A có thể xem toàn bộ lịch sử mượn/trả của thành viên B, bao gồm thông tin sách mượn, ngày mượn, trạng thái — đây là rò rỉ dữ liệu cá nhân. Lỗi bảo mật có mức độ ảnh hưởng cao trong hệ thống thực tế.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị MEM002 xem được phiếu mượn của MEM003 và MEM006)*

**Đề xuất xử lý:**
Thêm kiểm tra quyền truy cập trong hàm tra cứu phiếu mượn: khi người dùng đăng nhập là "Thành viên", chỉ cho phép tra cứu với chính mã thành viên của họ. Nếu nhập mã thành viên khác → từ chối truy cập hoặc tự động chuyển về xem phiếu của chính mình. Chỉ "Thủ thư" mới có quyền tra cứu mọi thành viên.

---

## BUG-07

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-07 |
| **TC liên quan** | TC-28 |
| **REQ liên quan** | REQ-03 |
| **Mức độ** | Medium |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Lọc sách theo thể loại "công nghệ" (chữ thường) trả về 0 kết quả — bộ lọc phân biệt hoa/thường (case-sensitive)

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập, đang ở tab **Sách**. Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Chuyển đến tab **Sách**.
2. Lọc thể loại **Công nghệ** (viết hoa) → hiển thị 8 sách — đúng.
3. Xóa bộ lọc → Lọc thể loại **công nghệ** (chữ thường).
4. Quan sát danh sách kết quả.

**Kết quả mong đợi:**
Hiển thị **8 sách** thể loại Công nghệ — giống hệt kết quả khi lọc bằng "Công nghệ" (chữ hoa). (Theo SRS REQ-03: "Tìm kiếm **KHÔNG phân biệt chữ hoa/thường** (case-insensitive)"). Quy tắc BR-10 cũng yêu cầu: "Tìm kiếm không phân biệt hoa/thường".

**Kết quả thực tế:**
Hiển thị thông báo **"Không tìm thấy sách nào"** — 0 kết quả. Bộ lọc chỉ hoạt động đúng khi nhập "Công nghệ" (đúng chữ hoa/thường như trong dữ liệu). Bộ lọc đang phân biệt hoa/thường (case-sensitive).

**Tác động:**
Người dùng nhập thể loại bằng chữ thường (phổ biến trong sử dụng thực tế) sẽ không tìm thấy sách dù thể loại đó tồn tại. Vi phạm yêu cầu case-insensitive của SRS REQ-03 và BR-10. Gây trải nghiệm người dùng kém — tưởng thư viện không có sách thể loại đó.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị "Không tìm thấy sách nào" khi lọc "công nghệ" chữ thường)*

**Đề xuất xử lý:**
Sửa logic lọc thể loại: so sánh giá trị lọc với thể loại sách sau khi đã chuyển cả 2 về cùng dạng (chữ thường hoặc chữ hoa) trước khi so sánh. Ví dụ: `filter.toLowerCase() === book.genre.toLowerCase()`. Đảm bảo bộ lọc hoạt động case-insensitive như yêu cầu SRS.
