# Test Summary — Báo cáo tổng hợp kiểm thử

> **Hướng dẫn**: Đây là hoạt động **Quality Assurance** — bạn đánh giá chất lượng tổng thể của phần mềm, không chỉ liệt kê lỗi.

---

## 1. Thông tin nhóm

| Mục | Thông tin |
|-----|----------|
| **Nhóm** | Nhóm 11 |
| **Lớp** | 252ICT2012.11 |
| **Ngày báo cáo** | 06/06/2026 |
| **Hệ thống kiểm thử** | https://stqa.rbc.vn — v1.0 |

---

## 2. Tổng quan kết quả

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

### Phân bổ theo nhóm chức năng

| Nhóm chức năng | TC | Pass | Fail | Bug | Đánh giá |
|---------------|-----|------|------|-----|---------|
| Đăng nhập (REQ-01) | 4 | 4 | 0 | 0 | ✅ Hoạt động ổn định, tất cả Pass |
| Xem danh sách sách (REQ-02) | 2 | 2 | 0 | 0 | ✅ Hiển thị đúng, cập nhật real-time |
| Tìm kiếm & Lọc (REQ-03) | 6 | 5 | 1 | BUG-07 | ⚠️ Tìm kiếm đúng, lọc thể loại phân biệt hoa/thường |
| Mượn sách (REQ-04) | 6 | 4 | 2 | BUG-01, BUG-02 | ❌ Lỗi nghiêm trọng: vượt giới hạn mượn, sai thông báo |
| Trả sách (REQ-05) | 2 | 1 | 1* | 0 | ⚠️ Trả sách hoạt động đúng, nhưng thiếu cảnh báo quá hạn (unimplemented) |
| Xử lý quá hạn (REQ-06) | 2 | 2 | 0 | 0 | ✅ Đánh dấu quá hạn đúng |
| Quản lý thành viên (REQ-07) | 3 | 0 | 3 | BUG-03, BUG-04, BUG-05 | ❌ Chức năng hoàn toàn hỏng: logic xác thực email đảo ngược |
| Tra cứu phiếu mượn (REQ-08) | 3 | 2 | 1 | BUG-06 | ❌ Lỗi bảo mật: thành viên xem được phiếu người khác khi tra cứu |

> *\* TC-19 Fail do tính năng cảnh báo quá hạn chưa được triển khai (unimplemented feature), không phải bug code.*

### Phân bổ bug theo mức độ

| Mức độ | Số lượng | Bug IDs |
|--------|---------|---------|
| High | 4 | BUG-01, BUG-03, BUG-04, BUG-06 |
| Medium | 3 | BUG-02, BUG-05, BUG-07 |

> **Ghi chú**: BUG-03, BUG-04, BUG-05 có chung nguyên nhân gốc (logic xác thực email bị đảo ngược) nhưng được báo cáo riêng vì mỗi bug biểu hiện khác nhau và ảnh hưởng đến người dùng theo cách khác nhau.

---

## 3. Kỹ thuật thiết kế đã sử dụng

| Kỹ thuật | Áp dụng cho REQ nào? | Số TC sử dụng | Giải thích cách áp dụng |
|----------|---------------------|---------------|------------------------|
| **Equivalence Partitioning (EP)** | REQ-01, REQ-02, REQ-03, REQ-04, REQ-05, REQ-06, REQ-07, REQ-08 | 22 | Chia dữ liệu đầu vào thành các phân vùng tương đương, mỗi phân vùng test 1 giá trị đại diện. Ví dụ: REQ-01 chia email thành 3 phân vùng (đúng/sai/rỗng); REQ-04 chia trạng thái sách thành 3 phân vùng (Có sẵn/Đang mượn/Thất lạc). |
| **Boundary Value Analysis (BVA)** | REQ-03, REQ-04, REQ-07 | 5 | Test tại ranh giới các phân vùng — nơi lỗi hay xảy ra nhất. Ví dụ: REQ-04 test giới hạn mượn tại biên 3 sách (BVA phát hiện BUG-01: off-by-one); REQ-07 test email tại biên hợp lệ/không hợp lệ (thiếu dấu chấm domain); REQ-03 test lọc thể loại tại biên hoa/thường. |
| **Decision Table** | REQ-04 | 1 bảng (18 quy tắc → 6 TC duy nhất) | Liệt kê tất cả tổ hợp 3 điều kiện (Trạng thái sách × Trạng thái thành viên × Số sách đang mượn) → 18 quy tắc. Sau khi loại trùng kết quả, 6 TC bao phủ tất cả kết quả duy nhất. Decision Table giúp đảm bảo không bỏ sót tổ hợp điều kiện nào. |

### Tại sao chọn kỹ thuật này cho REQ đó?

| REQ | Kỹ thuật chính | Lý do |
|-----|----------------|-------|
| REQ-01 (Đăng nhập) | EP | Input có các nhóm rời rạc rõ ràng: email đúng/sai/rỗng, MK đúng/sai/rỗng |
| REQ-03 (Tìm kiếm & Lọc) | EP + BVA | EP cho kết quả có/không; BVA cho biên hoa/thường (case-insensitive) |
| REQ-04 (Mượn sách) | EP + BVA + Decision Table | Nhiều điều kiện kết hợp → cần Decision Table; giới hạn 3 sách → BVA tại biên |
| REQ-07 (Quản lý thành viên) | EP + BVA | EP cho email hợp lệ/trùng; BVA cho biên hợp lệ/không hợp lệ (thiếu dấu chấm domain) |

---

## 4. Phân tích chất lượng phần mềm

### 4.1. Điểm mạnh

1. **Đăng nhập (REQ-01)**: Hoạt động ổn định — xác thực đúng email/mật khẩu, thông báo lỗi phù hợp cho từng trường hợp (email sai, MK sai, bỏ trống). Tỷ lệ Pass: 100%.

2. **Xem danh sách sách (REQ-02)**: Hiển thị đầy đủ 20 sách với thông tin chi tiết cho cả 2 vai trò. Cập nhật trạng thái real-time khi mượn/trả — không cần refresh. Tỷ lệ Pass: 100%.

3. **Xử lý quá hạn (REQ-06)**: Đánh dấu đúng các phiếu quá hạn khi Thủ thư nhấn "Kiểm tra quá hạn". Cả BR001 và BR003 (dueDate ≤ hôm nay) đều được đánh dấu chính xác. Tỷ lệ Pass: 100%.

4. **Tìm kiếm theo từ khóa (REQ-03)**: Tìm kiếm theo tên sách và tác giả hoạt động đúng, case-insensitive cho tìm kiếm văn bản (ví dụ: "flutter" = "Flutter" = "FLUTTER"). Lỗi chỉ xảy ra với bộ lọc thể loại.

5. **Chức năng trả sách cơ bản (REQ-05)**: Trả sách đúng hạn hoạt động tốt — sách chuyển về "Có sẵn", phiếu chuyển về "Đã trả" chính xác.

6. **Chức năng mượn sách cơ bản (REQ-04)**: Mượn sách cho thành viên hoạt động, sách "Có sẵn" → "Đang mượn", tạo phiếu đúng hạn 14 ngày. Trạng thái sách không có sẵn (Đang mượn, Thất lạc) hiển thị đúng — không có nút Mượn.

### 4.2. Điểm yếu

1. **Giới hạn mượn sách bị vô hiệu (BUG-01)**: Thành viên có thể mượn quá 3 sách — vi phạm quy tắc nghiệp vụ cốt lõi BR-01. Lỗi off-by-one (`>` thay `>=`) khiến giới hạn hoàn toàn vô hiệu khi thành viên đang mượn đúng 3 sách.

2. **Chức năng quản lý thành viên hoàn toàn hỏng (REQ-07)**: Tỷ lệ Pass **0%**. Logic xác thực email bị đảo ngược — hợp lệ bị từ chối (BUG-03), không hợp lệ được chấp nhận (BUG-04). Không thể thêm thành viên mới với email đúng định dạng → toàn bộ tính năng REQ-07 không sử dụng được.

3. **Sai thông báo lỗi cho thành viên Tạm ngưng (BUG-02)**: Hệ thống luôn hiển thị "hết hạn" cho cả Tạm ngưng lẫn Hết hạn — người dùng không phân biệt được lý do thực tế.

4. **Lỗi bảo mật — rò rỉ dữ liệu (BUG-06)**: Thành viên có thể xem phiếu mượn của người khác khi tra cứu bằng mã thành viên — vi phạm REQ-08 và BR-07. Đây là lỗi bảo mật nghiêm trọng, rò rỉ thông tin cá nhân.

5. **Sai thông báo email trùng (BUG-05)**: Email đã tồn tại hiện "không hợp lệ" thay vì "đã tồn tại" — gây nhầm lẫn cho người dùng.

6. **Bộ lọc thể loại phân biệt hoa/thường (BUG-07)**: Lọc "công nghệ" (chữ thường) trả về 0 kết quả — vi phạm yêu cầu case-insensitive của SRS REQ-03.

### 4.3. Tính năng chưa triển khai (unimplemented features — không phải bug)

1. **Cảnh báo quá hạn khi trả sách (REQ-05)**: SRS yêu cầu hiển thị cảnh báo rõ ràng khi trả sách quá hạn, nhưng hệ thống không hiển thị bất kỳ cảnh báo nào — tính năng chưa được xây dựng.

2. **Thủ thư mượn sách cho thành viên (SRS Section 1, 4.1)**: SRS quy định Thủ thư có quyền "mượn/trả sách cho thành viên", nhưng tab Sách không hiển thị nút Mượn khi đăng nhập bằng Thủ thư. TC-15/TC-16 phải điều chỉnh phương pháp thực thi (đăng nhập trực tiếp bằng tài khoản thành viên) để kiểm tra logic từ chối mượn sách.

---

## 5. Đề xuất ưu tiên sửa lỗi

> 💡 Tiêu chí ưu tiên: Kết hợp **severity** (mức độ nghiêm trọng kỹ thuật) và **priority** (mức độ ưu tiên kinh doanh). Ưu tiên cao cho: lỗi vi phạm quy tắc nghiệp vụ cốt lõi, lỗi bảo mật, lỗi làm tê liệt toàn bộ chức năng.

| Thứ tự | Bug | Mức độ | Lý do ưu tiên |
|--------|-----|--------|---------------|
| **1** | BUG-01 | High | **Vi phạm nghiệp vụ cốt lõi**: giới hạn 3 sách bị vô hiệu, thành viên mượn không giới hạn. Ảnh hưởng trực tiếp đến quy trình quản lý thư viện. Sửa đơn giản: đổi `>` thành `>=`. |
| **2** | BUG-03 + BUG-04 | High | **Chức năng hoàn toàn hỏng**: logic xác thực email đảo ngược → không thêm được thành viên mới. Hai bug chung nguyên nhân gốc, sửa 1 lần sẽ fix cả 2. |
| **3** | BUG-06 | High | **Lỗi bảo mật**: rò rỉ dữ liệu cá nhân — thành viên xem được lịch sử mượn của người khác khi tra cứu. Trong hệ thống thực tế, đây là vi phạm bảo mật dữ liệu cá nhân. |
| **4** | BUG-02 | Medium | **Sai thông báo**: Tạm ngưng hiện "hết hạn". Gây nhầm lẫn nhưng không ảnh hưởng khả năng từ chối mượn. Nên sửa cùng lúc với logic phân nhánh trạng thái thành viên. |
| **5** | BUG-05 | Medium | **Sai thông báo**: email trùng hiện "không hợp lệ" thay vì "đã tồn tại". Không ảnh hưởng tính năng (vẫn từ chối đúng), nhưng gây nhầm lẫn. Sửa cùng lúc với BUG-03/04. |
| **6** | BUG-07 | Medium | **Lọc case-sensitive**: vi phạm REQ-03 nhưng chỉ ảnh hưởng khi người dùng nhập chữ thường. Có workaround: nhập đúng chữ hoa như dữ liệu. Ảnh hưởng trải nghiệm người dùng hơn là nghiệp vụ. |

### Lộ trình sửa lỗi đề xuất

| Giai đoạn | Bug sửa | Thời gian ước tính | Lý do nhóm |
|-----------|---------|-------------------|------------|
| **Giai đoạn 1 — Khẩn cấp** | BUG-01, BUG-03+04 | 1–2 ngày | Sửa lỗi nghiệp vụ cốt lõi + khôi phục chức năng thêm thành viên |
| **Giai đoạn 2 — Bảo mật + Thông báo** | BUG-06, BUG-02, BUG-05 | 2–3 ngày | Khắc phục rò rỉ dữ liệu + cải thiện thông báo lỗi |
| **Giai đoạn 3 — UX** | BUG-07 | 1 ngày | Sửa lọc case-sensitive |

### Tính năng cần triển khai bổ sung (không phải bug — nhưng cần thiết cho SRS)

| Tính năng | SRS | Mức độ ưu tiên |
|-----------|-----|----------------|
| Cảnh báo quá hạn khi trả sách | REQ-05 | Cao — nghiệp vụ quản lý quá hạn |
| Thủ thư mượn sách cho thành viên | SRS Section 1, 4.1 | Cao — luồng nghiệp vụ chính |

---

## 6. Kết luận

Hệ thống Quản lý mượn sách Thư viện ABC **chưa sẵn sàng phát hành**. Với tỷ lệ Pass **71.4%** và **7 bug** (trong đó **4 bug mức High**), phần mềm có nhiều vấn đề nghiêm trọng ảnh hưởng trực tiếp đến chức năng nghiệp vụ cốt lõi:

- **Giới hạn mượn bị vô hiệu** (BUG-01) — mất kiểm soát số lượng sách
- **Không thêm được thành viên mới** (BUG-03/04) — toàn bộ REQ-07 tê liệt
- **Rò rỉ dữ liệu cá nhân** (BUG-06) — lỗi bảo mật nghiêm trọng

Ngoài ra, **2 tính năng quan trọng chưa được triển khai**: cảnh báo quá hạn khi trả sách (REQ-05) và Thủ thư mượn sách cho thành viên (SRS Section 1).

**Khuyến nghị**: Cần sửa tất cả bug mức High (giai đoạn 1 + 2) trước khi cân nhắc phát hành. Sau khi sửa, cần thực hiện **Regression Testing** — chạy lại toàn bộ 28 TC để đảm bảo các sửa lỗi không phát sinh lỗi mới.

---

## 7. Bài học rút ra

1. **Kỹ thuật thiết kế test case có hiệu quả thực tế**: BVA phát hiện BUG-01 (off-by-one tại biên giới hạn 3 sách) và BUG-04 (biên hợp lệ/không hợp lệ của email). Nếu chỉ test happy path, cả 2 bug này đều bị bỏ sót.

2. **Decision Table giúp bao phủ tổ hợp điều kiện**: REQ-04 có 3 điều kiện kết hợp → Decision Table đảm bảo không bỏ sót trường hợp nào. Nếu chỉ dùng EP, các tổ hợp như "sách Có sẵn + thành viên Tạm ngưng" có thể bị bỏ qua.

3. **Test Oracle mạnh mới phát hiện được bug**: BUG-02 chỉ phát hiện được vì expected result ghi cụ thể "phải chứa từ 'tạm ngưng'" (Strong Oracle). Nếu chỉ ghi "từ chối mượn sách" (Weak Oracle), bug sẽ lọt qua vì hệ thống đúng là từ chối — chỉ sai lý do.

4. **Quy trình kiểm thử cần linh hoạt**: Khi phát hiện Thủ thư không có nút Mượn (tính năng chưa triển khai), nhóm đã điều chỉnh cách thực thi TC-15/TC-16 bằng cách đăng nhập trực tiếp bằng tài khoản thành viên, thay vì bỏ qua test case.

5. **Bugs thường tập trung ở biên và điều kiện kết hợp**: 7/7 bug phát hiện ở các test case boundary hoặc negative, không có bug nào ở happy path đơn thuần. Điều này khẳng định tầm quan trọng của việc test không chỉ luồng chính mà còn luồng phụ và giá trị biên.

6. **Phân biệt bug vs tính năng chưa triển khai**: Trong quá trình kiểm thử, cần phân biệt rõ giữa lỗi code (hành vi sai với thiết kế) và tính năng chưa được xây dựng. Việc ghi nhận đúng giúp đội phát triển ưu tiên sửa lỗi thực sự trước, thay vì nhầm lẫn với việc triển khai tính năng mới.

---

## 8. Khai báo sử dụng AI (Tùy chọn)

> Nhóm đã sử dụng công cụ AI để hỗ trợ quá trình kiểm thử, sau đó tự đối chiếu lại với SRS.

| Công cụ AI | Dùng cho phần nào | Bạn đã kiểm tra/chỉnh sửa thế nào |
|------------|-------------------|-----------------------------------|
| Claude (Anthropic) | Hỗ trợ phân tích IDM, gợi ý vùng dữ liệu EP/BVA, rà soát format bug report | Tự đối chiếu mọi expected result với SRS; tự thực thi tất cả TC trên hệ thống thật; tự xác minh kết quả Pass/Fail; chỉnh sửa bug report cho phù hợp thực tế |
