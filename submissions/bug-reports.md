# Bug Reports — Báo cáo lỗi

> **Hướng dẫn**: Tạo 1 mục bug cho mỗi TC có kết quả **Fail**.
> Xem [examples/sample-bug-report.md](../examples/sample-bug-report.md) để hiểu cách viết bug report tốt.
> Mỗi bug cần: tiêu đề mô tả hành vi lỗi, bước tái hiện, expected vs actual, severity + giải thích.

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 11 |
| **Ngày báo cáo** | 06/06/2026 |

---

## BUG-01

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-01 |
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
Hệ thống từ chối mượn sách nhưng hiển thị thông báo **"hết hạn"** thay vì "tạm ngưng". Người dùng không thể phân biệt được lý do thực tế bị từ chối là do tạm ngưng hay hết hạn.

**Tác động:**
Thành viên bị tạm ngưng nhận thông báo sai, gây nhầm lẫn về lý do từ chối. Vi phạm quy tắc SRS REQ-04 yêu cầu thông báo lỗi phải mô tả đúng lý do. Thủ thư và thành viên không xác định được trạng thái tài khoản thực tế để có hành động khắc phục phù hợp.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị thông báo lỗi "hết hạn" khi MEM004 mượn sách)*

**Đề xuất xử lý:**
Kiểm tra lại logic xử lý thông báo lỗi trong hàm mượn sách. Đảm bảo khi trạng thái thành viên là "Tạm ngưng" (suspended), thông báo lỗi phải chứa từ "tạm ngưng". Có thể code đang gộp chung 2 trường hợp "Tạm ngưng" và "Hết hạn" vào cùng 1 nhánh thông báo.

---

## BUG-02

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-02 |
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

## BUG-03

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-03 |
| **TC liên quan** | TC-19 |
| **REQ liên quan** | REQ-05 |
| **Mức độ** | High |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Không hiển thị cảnh báo quá hạn khi trả sách quá hạn — vi phạm SRS REQ-05

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng Thủ thư, đã nhấn "Kiểm tra quá hạn" để BR001 được đánh dấu "Quá hạn". Sau đó đăng nhập bằng MEM002.

**Bước tái hiện:**
1. Đăng nhập bằng Thủ thư (`librarian@library.com` / `admin123`).
2. Chuyển đến tab **Mượn / Trả**.
3. Nhấn nút **Kiểm tra quá hạn** → BR001 (MEM002, BOOK003, dueDate 15/09/2024) được đánh dấu "Quá hạn".
4. Đăng xuất. Đăng nhập bằng MEM002 (`ba.nguyen@email.com` / `password123`).
5. Chuyển đến tab **Mượn / Trả**.
6. Tìm phiếu BR001 (BOOK003, trạng thái "Quá hạn").
7. Nhấn nút **Trả** trên BR001.

**Kết quả mong đợi:**
Trước khi trả, hệ thống hiển thị **cảnh báo quá hạn rõ ràng** (ví dụ: dialog hoặc thông báo "Sách đã quá hạn"). Sau khi xác nhận: trả thành công, BOOK003 → "Có sẵn". (Theo SRS REQ-05: "Nếu trả quá hạn → hệ thống phải hiển thị cảnh báo quá hạn").

**Kết quả thực tế:**
Trả sách thành công ngay lập tức, BOOK003 → "Có sẵn". **Không hiển thị bất kỳ cảnh báo quá hạn nào** trước hoặc trong quá trình trả.

**Tác động:**
Vi phạm yêu cầu SRS REQ-05 và quy tắc nghiệp vụ BR-06. Thành viên trả sách quá hạn mà không được cảnh báo — không nhận thức được vi phạm, không có động lực trả sách đúng hạn trong tương lai. Mất tính năng quan trọng giúp thư viện quản lý việc quá hạn.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị trả BR001 thành công mà không có cảnh báo)*

**Đề xuất xử lý:**
Thêm logic hiển thị cảnh báo quá hạn (dialog hoặc banner) khi người dùng nhấn "Trả" trên phiếu mượn có trạng thái "Quá hạn". Cảnh báo phải hiển thị trước khi hoàn tất việc trả sách.

---

## BUG-04

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-04 |
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
Không thể thêm bất kỳ thành viên mới nào có email hợp lệ — **chức năng quản lý thành viên hoàn toàn không sử dụng được**. Đây là lỗi nghiêm trọng làm tê liệt toàn bộ tính năng REQ-07. Nguyên nhân khả thi: logic xác thực email bị đảo ngược hoặc sai biểu thức chính quy (regex).

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị thông báo "Email không hợp lệ" với email hợp lệ)*

**Đề xuất xử lý:**
Kiểm tra lại logic xác thực email trong form thêm thành viên. Biểu thức xác thực có thể bị sai hoặc bị đảo ngược — chấp nhận email không hợp lệ (thiếu dấu chấm domain) nhưng từ chối email hợp lệ. Sửa regex/logic kiểm tra email để đảm bảo: email có `@` VÀ có `.` trong phần domain thì hợp lệ.

---

## BUG-05

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-05 |
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
Hệ thống **chấp nhận** email `user@domain`, tạo thành viên mới thành công (MEM007). Không hiển thị thông báo lỗi.

**Tác động:**
Cho phép tạo thành viên với email không hợp lệ, vi phạm quy tắc xác thực SRS REQ-07 và BR-08. Dữ liệu thành viên bị sai — email không hợp lệ có thể gây lỗi khi cần gửi thông báo hoặc liên lạc. Kết hợp với BUG-04, logic xác thực email bị **đảo ngược hoàn toàn**: hợp lệ → từ chối, không hợp lệ → chấp nhận.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị tạo thành viên thành công với email `user@domain`)*

**Đề xuất xử lý:**
Sửa logic xác thực email — đảo ngược điều kiện kiểm tra. Đảm bảo email `user@domain` (thiếu dấu `.` trong domain) bị từ chối, và email `nguyenvanmoi@email.com` (có đủ `@` và `.` trong domain) được chấp nhận. Có thể cần viết lại biểu thức regex xác thực email hoàn toàn.

---

## BUG-06

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-06 |
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

## BUG-07

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-07 |
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
4. Trong phần tra cứu phiếu mượn, nhập mã thành viên **MEM003**.
5. Quan sát kết quả — thấy phiếu BR002 và BR005 của MEM003.
6. Nhập mã thành viên **MEM006**.
7. Quan sát kết quả — thấy phiếu BR003 của MEM006.

**Kết quả mong đợi:**
MEM002 **không xem được** phiếu mượn của MEM003 hoặc MEM006. Theo SRS REQ-08: "Thành viên chỉ xem phiếu mượn **của chính mình**. **KHÔNG được xem phiếu mượn của thành viên khác.**"

**Kết quả thực tế:**
MEM002 có thể xem đầy đủ phiếu mượn của MEM003 (BR002, BR005 — "Đã trả") và MEM006 (BR003 — "Đang mượn"). Không có bất kỳ hạn chế quyền truy cập nào.

**Tác động:**
Vi phạm nghiêm trọng quy tắc bảo mật phiếu mượn (BR-07 và SRS REQ-08). Thành viên A có thể xem toàn bộ lịch sử mượn/trả của thành viên B, bao gồm thông tin sách mượn, ngày mượn, trạng thái — đây là rò rỉ dữ liệu cá nhân. Lỗi bảo mật có mức độ ảnh hưởng cao trong hệ thống thực tế.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị MEM002 xem được phiếu mượn của MEM003 và MEM006)*

**Đề xuất xử lý:**
Thêm kiểm tra quyền truy cập trong hàm tra cứu phiếu mượn: khi người dùng đăng nhập là "Thành viên", chỉ cho phép tra cứu với chính mã thành viên của họ. Nếu nhập mã thành viên khác → từ chối truy cập hoặc tự động chuyển về xem phiếu của chính mình. Chỉ "Thủ thư" mới có quyền tra cứu mọi thành viên.

---

## BUG-08

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-08 |
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
2. Nhập hoặc chọn thể loại `"công nghệ"` (chữ thường) trong bộ lọc thể loại.
3. Quan sát danh sách kết quả.

**Kết quả mong đợi:**
Hiển thị **8 sách** thể loại Công nghệ: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011 — giống hệt kết quả khi lọc bằng "Công nghệ" (chữ hoa). (Theo SRS REQ-03: "Tìm kiếm **KHÔNG phân biệt chữ hoa/thường** (case-insensitive)"). Quy tắc BR-10 cũng yêu cầu: "Tìm kiếm không phân biệt hoa/thường".

**Kết quả thực tế:**
Hiển thị thông báo **"Không tìm thấy sách nào"** — 0 kết quả. Bộ lọc chỉ hoạt động đúng khi nhập "Công nghệ" (đúng chữ hoa/thường như trong dữ liệu). Bộ lọc đang phân biệt hoa/thường (case-sensitive).

**Tác động:**
Người dùng nhập thể loại bằng chữ thường (phổ biến trong sử dụng thực tế) sẽ không tìm thấy sách dù thể loại đó tồn tại. Vi phạm yêu cầu case-insensitive của SRS REQ-03 và BR-10. Gây trải nghiệm người dùng kém — tưởng thư viện không có sách thể loại đó.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình hiển thị "Không tìm thấy sách nào" khi lọc "công nghệ" chữ thường)*

**Đề xuất xử lý:**
Sửa logic lọc thể loại: so sánh giá trị lọc với thể loại sách sau khi đã chuyển cả 2 về cùng dạng (chữ thường hoặc chữ hoa) trước khi so sánh. Ví dụ: `filter.toLowerCase() === book.genre.toLowerCase()`. Đảm bảo bộ lọc hoạt động case-insensitive như yêu cầu SRS.

---

## BUG-09

| Thuộc tính | Chi tiết |
|-----------|---------|
| **Mã lỗi** | BUG-09 |
| **TC liên quan** | Phát hiện bổ sung khi thực thi TC-15 |
| **REQ liên quan** | REQ-04 |
| **Mức độ** | High |
| **Người phát hiện** | Nhóm 11 |
| **Ngày phát hiện** | 06/06/2026 |
| **Trạng thái** | Open |

**Tiêu đề:**
Thủ thư không có nút "Mượn" trên tab Sách — không thể mượn sách cho thành viên theo SRS

**Môi trường:**
- Trình duyệt: Chrome (bản mới nhất)
- Hệ điều hành: Windows 11
- Ngôn ngữ giao diện: Tiếng Việt

**Điều kiện tiên quyết:**
Đã đăng nhập bằng tài khoản Thủ thư (`librarian@library.com` / `admin123`). Dữ liệu đã reset (F5).

**Bước tái hiện:**
1. Đăng nhập bằng Thủ thư: `librarian@library.com` / `admin123`.
2. Chuyển đến tab **Sách**.
3. Quan sát các sách trong danh sách — kiểm tra xem có nút **Mượn** trên mỗi sách không.
4. Chuyển đến tab **Mượn / Trả**.
5. Kiểm tra xem có nút hoặc form **"Mượn sách"** / **"Tạo phiếu mượn"** cho thành viên không.

**Kết quả mong đợi:**
Thủ thư thấy nút **Mượn** trên các sách ở trạng thái "Có sẵn" trong tab Sách, HOẶC có form tạo phiếu mượn cho thành viên trong tab Mượn / Trả. (Theo SRS Section 1: Thủ thư có quyền "mượn/trả sách cho thành viên". Theo SRS Section 4.1: tab Sách có "nút mượn" cho "Tất cả" — bao gồm cả Thủ thư).

**Kết quả thực tế:**
Tab **Sách**: Không có nút **Mượn** trên bất kỳ sách nào khi đăng nhập bằng Thủ thư. Tab **Mượn / Trả**: Chỉ có nút **Trả** sách cho các phiếu đang mượn, không có form/chức năng mượn sách cho thành viên. Thủ thư **hoàn toàn không thể mượn sách cho bất kỳ thành viên nào**.

**Tác động:**
Thủ thư mất hoàn toàn khả năng mượn sách cho thành viên — đây là **chức năng nghiệp vụ cốt lõi** theo SRS. Các TC-15, TC-16 (kiểm tra thành viên Tạm ngưng / Hết hạn mượn sách) không thể thực hiện theo đúng luồng Thủ thư, phải workaround bằng cách đăng nhập trực tiếp bằng tài khoản thành viên. Vi phạm nghiêm trọng quyền truy cập được định nghĩa trong SRS.

**Minh chứng:**
*(Đính kèm ảnh chụp màn hình tab Sách khi đăng nhập Thủ thư — không có nút Mượn)*

**Đề xuất xử lý:**
Thêm nút **Mượn** trên tab Sách cho vai trò Thủ thư, với luồng: nhấn Mượn → chọn thành viên (dropdown hoặc nhập mã) → xác nhận mượn sách cho thành viên đó. Hoặc thêm form "Tạo phiếu mượn" trong tab Mượn / Trả cho phép Thủ thư nhập mã thành viên + mã sách.
