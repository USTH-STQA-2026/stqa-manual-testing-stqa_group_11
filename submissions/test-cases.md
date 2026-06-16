# Test Cases

| Information | |
|---|---|
| **Team** | Group 11 |
| **Date Created** | 27/05/2026 |
| **System** | https://stqa.rbc.vn |
| **Reference** | SRS v1.0 |

---

## Step 1: Input Domain Modeling (IDM)

> 📖 **Textbook:** Chapter 6 — *Input Domain Modeling*, Paul Ammann & Jeff Offutt.
>
> **Before writing Test Cases**, the team **must** analyze the input domain using the IDM tables below.
> Each feature must identify: **Characteristic**, **Block (Partition)**, and **Representative Value**.

### IDM — Login (REQ-01)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| Does email exist in DB? | Yes | `librarian@library.com` | Login successful |
| | No | `noone@email.com` | Message: "Member not found" |
| Is password correct? | Correct | `admin123` | Login successful |
| | Incorrect | `wrongpass` | Message: "Incorrect password" |
| Is input field empty? | Not empty | (any value) | Normal processing |
| | Empty | `""` | Message: "Please enter email and password" |

### IDM — View Book List (REQ-02)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| User role? | Librarian | `librarian@library.com` | Full book list visible |
| | Member | `ba.nguyen@email.com` | Full book list visible |
| Real-time status update? | After borrowing | Borrow BOOK001 → check list | BOOK001 changes to "Borrowed" |
| | After returning | Return BOOK003 → check list | BOOK003 changes to "Available" |

### IDM — Search & Filter Books (REQ-03)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| Does keyword exist in DB? | Yes (book title) | `"Flutter"` | Shows books containing "Flutter" |
| | Yes (author name) | `"Nguyen"` | Shows books by author Nguyen |
| | No | `"XYZ123"` | Empty list, message "No books found" |
| Case sensitivity? | Lowercase | `"flutter"` | Same result as "Flutter" (case-insensitive) |
| | Uppercase | `"FLUTTER"` | Same result as "Flutter" (case-insensitive) |
| Filter by genre? | Genre with books | `"Technology"` | Shows Technology genre books |
| | Genre — case check | `"technology"` | Same result as "Technology" (case-insensitive per SRS) |

### IDM — Borrow Book (REQ-04)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| Book status? | Available | BOOK001 | Borrowing allowed |
| | Borrowed | BOOK003 | Rejected, message: book not available |
| | Lost | BOOK007 | Rejected, message: book not available |
| Member status? | Active | MEM002 | Borrowing allowed |
| | Suspended | MEM004 | Rejected, message contains "suspended" |
| | Expired | MEM005 | Rejected, message contains "expired" |
| Number of books currently borrowed? | < 3 (BVA: 2) | Member currently borrowing 2 books | Borrowing allowed |
| | = 3 (BVA: limit) | Member currently borrowing 3 books | Rejected, message: limit exceeded |

### IDM — Return Book (REQ-05)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| Book currently borrowed by member? | Currently borrowed | BR003 (MEM006 borrowed BOOK013) | Return successful, book → "Available" |
| | Not borrowed | Already returned book (BR002) | Cannot return |
| Returning overdue? | Overdue (dueDate ≤ today) | BR001 (MEM002, due 15/09/2024) | Show overdue warning + return successful |
| | On time | Book not yet overdue | Return successful, no warning |

### IDM — Overdue Handling (REQ-06)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| Is record overdue? | Yes (dueDate ≤ today) | BR001 (due 15/09/2024) | Marked "Overdue" after Librarian clicks check |
| | No (dueDate > today) | BR003 (due 15/10/2024) | Not marked overdue |
| Who checks? | Librarian | `librarian@library.com` | Sees all overdue records |
| | Member | `ba.nguyen@email.com` | Sees only own overdue records |

### IDM — Member Management (REQ-07)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| Valid email? | Valid (has `@` AND `.` in domain) | `newmember@email.com` | Created successfully |
| | Invalid — missing dot in domain | `user@domain` | Email invalid error message |
| | Invalid — missing `@` | `userdomain.com` | Email invalid error message |
| Email already exists? | Not yet exists | `newperson@test.com` | Created successfully |
| | Already exists | `ba.nguyen@email.com` | Email already exists error message |
| Required fields? | Complete | Full name, email, phone | Created successfully |
| | Missing full name | No name entered | Error message |

### IDM — Borrow Record Lookup (REQ-08)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
|---|---|---|---|
| User role viewing records? | Librarian | `librarian@library.com` | Sees all records of all members |
| | Member | `ba.nguyen@email.com` | Sees only own records |
| Member viewing another's records? | Viewing own records | MEM002 views BR001, BR004 | Displayed |
| | Viewing another's records | MEM002 cannot view BR003 (MEM006) | Other member's records not shown |
| Record information? | Complete display | BR001 | Record ID, book borrowed, borrow date, due date, status |

---

## Step 2: Decision Table (REQ-04: Borrow Book)

> 📖 **Bonus B2**: Complete Decision Table for the Borrow Book feature.
> 3 combined conditions: Book Status × Member Status × Number of Books Borrowed.

### Conditions

| Code | Condition | Values |
|----|-----------|---------|
| **C1** | Book status | Available (Y) / Not available (N: Borrowed or Lost) |
| **C2** | Member status | Active (Y) / Not active (N: Suspended or Expired) |
| **C3** | Number of books borrowed < 3? | Yes (Y) / No (N) |

### Full Decision Table

| Rule | C1: Book Available? | C2: Member Active? | C3: Books < 3? | Result | Error Message |
|---------|:-:|:-:|:-:|:--------:|---------------|
| **R1** | Y | Y | Y | ✅ Allow borrow | — |
| **R2** | Y | Y | N | ❌ Reject | Maximum borrow limit reached (3 books) |
| **R3** | Y | N (Suspended) | Y | ❌ Reject | Member is suspended. Cannot borrow books. |
| **R4** | Y | N (Suspended) | N | ❌ Reject | Member is suspended. Cannot borrow books. |
| **R5** | Y | N (Expired) | Y | ❌ Reject | Member account has expired. Cannot borrow books. |
| **R6** | Y | N (Expired) | N | ❌ Reject | Member account has expired. Cannot borrow books. |
| **R7** | N (Borrowed) | Y | Y | ❌ Reject | Book is not available for borrowing. |
| **R8** | N (Borrowed) | Y | N | ❌ Reject | Book is not available for borrowing. |
| **R9** | N (Borrowed) | N (Suspended) | Y | ❌ Reject | Book is not available for borrowing. |
| **R10** | N (Borrowed) | N (Suspended) | N | ❌ Reject | Book is not available for borrowing. |
| **R11** | N (Borrowed) | N (Expired) | Y | ❌ Reject | Book is not available for borrowing. |
| **R12** | N (Borrowed) | N (Expired) | N | ❌ Reject | Book is not available for borrowing. |
| **R13** | N (Lost) | Y | Y | ❌ Reject | Book is not available for borrowing. |
| **R14** | N (Lost) | Y | N | ❌ Reject | Book is not available for borrowing. |
| **R15** | N (Lost) | N (Suspended) | Y | ❌ Reject | Book is not available for borrowing. |
| **R16** | N (Lost) | N (Suspended) | N | ❌ Reject | Book is not available for borrowing. |
| **R17** | N (Lost) | N (Expired) | Y | ❌ Reject | Book is not available for borrowing. |
| **R18** | N (Lost) | N (Expired) | N | ❌ Reject | Book is not available for borrowing. |

### Rule → Test Case Mapping

| Rule | Corresponding TC | Notes |
|---------|-------------|---------|
| R1 | TC-12 | Happy path |
| R2 | TC-17 | BVA boundary |
| R3 | TC-15 | Suspended member |
| R5 | TC-16 | Expired member |
| R7 | TC-13 | Borrowed book |
| R13 | TC-14 | Lost book |
| R4, R6, R9–R12, R14–R18 | Results overlap with R3/R5/R7/R13 — no separate TC needed | Stronger condition already covered |

> **Note**: Rules R4, R6, R9–R18 overlap with already-tested rules (R3, R5, R7, R13). Only 6 TCs are needed to cover all 18 rules.

---

## Step 3: Test Cases

### REQ-01: Login

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-01 | Successful login — Librarian | Login page open, data reset | 1. Enter email `librarian@library.com`. 2. Enter password `admin123`. 3. Click **Login**. | Email: `librarian@library.com`, PW: `admin123` | Redirected to home page. AppBar displays name "Nguyen Thu Thu" and role "Librarian". | REQ-01 | EP |
| TC-02 | Failed login — Email not found | Login page open | 1. Enter email `nobody@test.com`. 2. Enter password `anything`. 3. Click **Login**. | Email: `nobody@test.com`, PW: `anything` | Page does not change. Error message: "Member not found". | REQ-01 | EP |
| TC-03 | Failed login — Wrong password | Login page open | 1. Enter email `ba.nguyen@email.com`. 2. Enter password `wrongpassword`. 3. Click **Login**. | Email: `ba.nguyen@email.com`, PW: `wrongpassword` | Page does not change. Error message: "Incorrect password". | REQ-01 | EP |
| TC-04 | Failed login — Both fields empty | Login page open | 1. Leave email field empty. 2. Leave password field empty. 3. Click **Login**. | Email: `""`, PW: `""` | Page does not change. Error message: "Please enter email and password". | REQ-01 | EP |

### REQ-02: View Book List

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-05 | View book list — Both roles can view | Logged in (try Librarian and Member separately) | 1. Login as Librarian → go to "Books" tab. 2. Logout. 3. Login as Member (`ba.nguyen@email.com`) → go to "Books" tab. | N/A | Both times show 20 books. Each book shows: title, author, genre, publication year, status (Available / Borrowed). | REQ-02 | EP |
| TC-06 | Real-time book status update after borrowing | Logged in as MEM002 (`ba.nguyen@email.com`). Data reset. | 1. Go to "Books" tab. Note BOOK001 status "Available". 2. Borrow BOOK001. 3. Go back to "Books" tab. Check BOOK001 status. | Book: BOOK001 | After step 2: BOOK001 changes to "Borrowed". Update appears immediately without page refresh. | REQ-02 | EP |

### REQ-03: Search & Filter Books

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-07 | Search by book title — Results found | Logged in, on "Books" tab | 1. Enter keyword `"Flutter"` in search box. | Keyword: `"Flutter"` | Shows book "Lap trinh Flutter co ban" (BOOK001, Nguyen Minh Duc, Technology, 2023). | REQ-03 | EP |
| TC-08 | Search by author — Results found | Logged in, on "Books" tab | 1. Enter keyword `"Nguyen Minh Duc"` in search box. | Keyword: `"Nguyen Minh Duc"` | Shows 2 books by Nguyen Minh Duc: BOOK001 and BOOK009. | REQ-03 | EP |
| TC-09 | Search — No results found | Logged in, on "Books" tab | 1. Enter keyword `"xyz_not_exist"` in search box. | Keyword: `"xyz_not_exist"` | Empty list. Message: "No books found". | REQ-03 | EP |
| TC-10 | Case-insensitive search | Logged in, on "Books" tab | 1. Enter `"flutter"` (lowercase). 2. Clear, enter `"FLUTTER"` (uppercase). | Keywords: `"flutter"`, `"FLUTTER"` | Both show same result as `"Flutter"` — BOOK001. Search is case-insensitive per SRS REQ-03. | REQ-03 | BVA |
| TC-11 | Filter books by genre | Logged in, on "Books" tab | 1. Select genre "Technology" in filter. | Genre: `"Technology"` | Shows 8 Technology books: BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011. | REQ-03 | EP |

### REQ-04: Borrow Book

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-12 | Successful borrow — Available book, Active member (DT-R1) | Logged in as MEM002 (`ba.nguyen@email.com`, Active). Data reset. | 1. Go to "Books" tab. 2. Select BOOK004 (Available). 3. Click **Borrow**. | Member: MEM002, Book: BOOK004 | Borrow successful. New borrow record created, due date = borrow date + 14 days. BOOK004 changes to "Borrowed". MEM002's borrowed count increases to 2. | REQ-04 | EP, DT-R1 |
| TC-13 | Borrow already-borrowed book — Rejected (DT-R7) | Logged in. Data reset (BOOK003 currently borrowed by MEM002). | 1. Go to "Books" tab. 2. Find BOOK003 (status "Borrowed"). 3. Click **Borrow**. | Book: BOOK003 (Borrowed) | Borrow rejected. Error message describing book is not available. | REQ-04 | EP, DT-R7 |
| TC-14 | Borrow lost book — Rejected (DT-R13) | Logged in. Data reset (BOOK007 status "Lost"). | 1. Go to "Books" tab. 2. Find BOOK007 (Lost). 3. Click **Borrow**. | Book: BOOK007 (Lost) | Borrow rejected. Error message describing book is not available. | REQ-04 | EP, DT-R13 |
| TC-15 | Suspended member borrows book — Rejected (DT-R3) | Logged in as `cu.le@email.com` / `password123` (MEM004, **Suspended**). Data reset. | 1. Login as `cu.le@email.com`. 2. Go to "Books" tab. 3. Find BOOK001 (Available). 4. Click **Borrow** on BOOK001. | Member: MEM004 (Suspended), Book: BOOK001 | Borrow rejected. Error message contains "suspended" — must NOT show "expired" (per SRS REQ-04: must describe correct reason). | REQ-04 | EP, DT-R3 |
| TC-16 | Expired member borrows book — Rejected (DT-R5) | Logged in as `binh.pham@email.com` / `password123` (MEM005, **Expired**). Data reset. | 1. Login as `binh.pham@email.com`. 2. Go to "Books" tab. 3. Find BOOK001 (Available). 4. Click **Borrow** on BOOK001. | Member: MEM005 (Expired), Book: BOOK001 | Borrow rejected. Error message contains "expired" — must NOT show "suspended" (per SRS REQ-04: must describe correct reason). | REQ-04 | EP, DT-R5 |
| TC-17 | Borrow book — Maximum 3-book limit (BVA boundary, DT-R2) | Logged in as MEM003 (`dam.tran@email.com`, Active, currently borrowing 0 books). Data reset. | 1. Borrow BOOK001 → note (borrowing 1). 2. Borrow BOOK002 → note (borrowing 2). 3. Borrow BOOK004 → note (borrowing 3). 4. Borrow BOOK005 → attempt 4th book. | Member: MEM003, BVA boundary: 2 books → 3 books | Steps 1–3: Borrow successful. Step 4: Rejected, error message "Maximum borrow limit reached (3 books)". | REQ-04 | BVA, DT-R2 |

> **📌 Execution note for TC-15/TC-16**: SRS requires the Librarian to have the right to "borrow books for members" (Section 1). However, the system **does not display a Borrow button** when logged in as Librarian — this feature is not yet implemented (unimplemented feature). Therefore, TC-15 and TC-16 were executed by **logging in directly with the member account** (MEM004/MEM005). This approach is still valid because SRS REQ-01 only checks email + password for login, and REQ-04 specifies that borrow rejection applies at the borrowing step — regardless of who performs the action.

### REQ-05: Return Book

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-18 | Return book on time — Status updated | Logged in as MEM006 (`biet.hoang@email.com`). Data reset (MEM006 borrowing BOOK013 via BR003). | 1. Go to "Borrow / Return" tab. 2. Select borrow record BR003. 3. Click **Return**. | Record: BR003, Book: BOOK013 | Return successful. BOOK013 changes to "Available". BR003 changes to "Returned". No overdue warning shown. | REQ-05 | EP |
| TC-19 | Return overdue book — Warning displayed | Logged in as MEM002 (`ba.nguyen@email.com`). Data reset. Librarian has clicked "Check Overdue" (BR001 marked overdue). | 1. Go to "Borrow / Return" tab. 2. Select borrow record BR001 (overdue). 3. Click **Return**. | Record: BR001, Book: BOOK003 | Overdue warning displayed clearly before return. After confirmation: return successful, BOOK003 → "Available", BR001 → "Returned". | REQ-05 | EP |

### REQ-06: Overdue Handling

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-20 | Librarian checks overdue — Records marked overdue | Logged in as Librarian (`librarian@library.com`). Data reset (BR001 dueDate 15/09/2024 ≤ today). | 1. Go to "Borrow / Return" tab. 2. Click **Check Overdue**. 3. Check BR001 status. | N/A | BR001 (dueDate 15/09/2024 ≤ current date) is marked "Overdue". Records with dueDate > today are not marked. | REQ-06 | EP |
| TC-21 | Member views own overdue records | Logged in as MEM002 (`ba.nguyen@email.com`). Librarian has clicked "Check Overdue". | 1. Go to "Borrow / Return" tab. 2. View "My borrow records". | N/A | MEM002 sees BR001 (BOOK003) with status "Overdue". Does not see records of other members. | REQ-06 | EP |

### REQ-07: Member Management

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-22 | Add new valid member | Logged in as Librarian (`librarian@library.com`). Data reset. | 1. Go to "Members" tab. 2. Click **Add Member**. 3. Enter full name `Nguyen Van Moi`. 4. Enter email `nguyenvanmoi@email.com`. 5. Enter phone `0901234567`. 6. Click **Create** / **Save**. | Name: `Nguyen Van Moi`, Email: `nguyenvanmoi@email.com`, Phone: `0901234567` | Created successfully. New member appears in list with status "Active". | REQ-07 | EP |
| TC-23 | Add member — Invalid email, missing dot in domain (BVA) | Logged in as Librarian. Data reset. | 1. Go to "Members" tab. 2. Click **Add Member**. 3. Enter name `Test User`. 4. Enter email `user@domain` (no `.` in domain). 5. Enter phone `0901234567`. 6. Click **Create** / **Save**. | Email: `user@domain` (BVA: valid/invalid boundary) | Not created. Error message: invalid email. Per SRS REQ-07: email must have `@` AND `.` in domain. | REQ-07 | BVA |
| TC-24 | Add member — Email already exists | Logged in as Librarian. Data reset. | 1. Go to "Members" tab. 2. Click **Add Member**. 3. Enter name `Duplicate Email`. 4. Enter email `ba.nguyen@email.com` (already exists). 5. Enter phone `0901234567`. 6. Click **Create** / **Save**. | Email: `ba.nguyen@email.com` (already exists) | Not created. Error message: email already exists. | REQ-07 | EP |

### REQ-08: Borrow Record Lookup

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-25 | Librarian views all borrow records | Logged in as Librarian (`librarian@library.com`). Data reset. | 1. Go to "Borrow / Return" tab. 2. Look up borrow records. | N/A | Shows all 5 borrow records: BR001–BR005. Each record shows: record ID, book borrowed, borrow date, due date, status. | REQ-08 | EP |
| TC-26 | Member views only own borrow records | Logged in as MEM002 (`ba.nguyen@email.com`). Data reset. | 1. Go to "Borrow / Return" tab. 2. View "My borrow records". | N/A | Shows only MEM002's records: BR001 (BOOK003, Borrowing) and BR004 (BOOK005, Returned). Other members' records not shown. | REQ-08 | EP |
| TC-27 | Member cannot view another member's borrow records | Logged in as MEM002 (`ba.nguyen@email.com`). Data reset. | 1. Go to "Borrow / Return" tab. 2. Try to look up records by member ID MEM003 or MEM006. | Member ID: MEM003 or MEM006 | MEM003/MEM006's borrow records not displayed. Per SRS REQ-08: members must NOT view other members' borrow records. | REQ-08 | EP |

### REQ-03 (additional): Case-Insensitive Genre Filter

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|------------|-----------------|-----|-----------|
| TC-28 | Filter books by genre — Case-insensitive | Logged in, on "Books" tab | 1. Enter or select genre `"technology"` (lowercase) in filter. | Genre: `"technology"` (lowercase) | Shows same result as filtering "Technology": 8 Technology books (BOOK001, 002, 003, 005, 008, 009, 010, 011). Filter must be case-insensitive per SRS REQ-03. | REQ-03 | BVA |

---

## Summary

| Feature Group | TC Count | REQ Covered | IDM Technique Applied |
|----------------|-------|---------|----------------------|
| Login | 4 | REQ-01 | EP (3 partitions: correct/wrong/empty email, correct/wrong/empty password) |
| View Book List | 2 | REQ-02 | EP (Librarian/Member role, real-time update) |
| Search & Filter | 5 | REQ-03 | EP (results found/not found), BVA (case boundary) |
| Borrow Book | 6 | REQ-04 | EP (book/member status), BVA (3-book limit), Decision Table (R1–R13) |
| Return Book | 2 | REQ-05 | EP (on time/overdue) |
| Overdue Handling | 2 | REQ-06 | EP (overdue/not overdue, role checking) |
| Member Management | 3 | REQ-07 | EP (valid/duplicate email), BVA (valid/invalid email boundary) |
| Borrow Record Lookup | 3 | REQ-08 | EP (Librarian sees all / Member sees own only) |
| Case-insensitive Filter | 1 | REQ-03 | BVA (lowercase boundary) |
| **Total** | **28** | **REQ-01 → REQ-08** | **EP + BVA + Decision Table** |
