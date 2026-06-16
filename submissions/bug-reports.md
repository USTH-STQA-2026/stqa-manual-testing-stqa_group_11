# Bug Reports

> **Instructions**: Create one bug report for each TC with a **Fail** result (due to code defects, not unimplemented features).
> See [examples/sample-bug-report.md](../examples/sample-bug-report.md) for how to write a good bug report.
> Each bug needs: a title describing the erroneous behavior, steps to reproduce, expected vs actual, severity + explanation.

| Information | |
|---|---|
| **Team** | Team 11 |
| **Report Date** | 06/06/2026 |

**Environment:**
- Browser: Chrome (latest version)
- Operating System: Windows 11
- Interface Language: Vietnamese

> **📌 Note**: TC-19 (no overdue warning when returning a book) and the "Librarian has no Borrow button" issue are **unimplemented features**, not code bugs. Therefore, no bug reports are created for these cases.

---

## BUG-01: Able to borrow a 4th book — exceeds maximum limit of 3 books/member (off-by-one error: `>` instead of `>=`)

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-01 |
| **Related TC** | TC-17 |
| **Related REQ** | REQ-04 |
| **Severity** | High |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Logged in with an active member account with 0 books currently borrowed. Data has been reset (F5).

**Steps to Reproduce:**
1. Enter email `dam.tran@email.com` (MEM003, Active, 0 books currently borrowed) and password `password123`.
2. Click **Login**.
3. Go to **Books** tab.
4. Borrow BOOK001 → success (currently borrowing 1 book).
5. Borrow BOOK002 → success (currently borrowing 2 books).
6. Borrow BOOK004 → success (currently borrowing 3 books).
7. Borrow BOOK005 (attempt to borrow a 4th book).

**Expected Result:**
Steps 4–6: Borrow successful. Step 7: System **rejects** the 4th borrow. Displays error message "Maximum borrow limit reached (3 books)". (Per SRS REQ-04: maximum 3 books/member at the same time).

**Actual Result:**
Steps 4–6: Borrow successful. Step 7: System **allows borrowing the 4th book** — no rejection, no error message displayed. Member MEM003 is currently borrowing 4 books simultaneously.

**Impact:**
Serious violation of core business rule (BR-01): maximum 3 books/member limit. Allows members to borrow unlimited books, causing loss of control over the number of books per person. Probable cause: code uses operator `>` instead of `>=` when comparing current borrow count with the limit (off-by-one error).

**Evidence:**
![BUG-01: MEM003 currently borrowing 4 books](evidences/BUG-01.png)

**Suggested Fix:**
Change the borrow limit check condition from `if (currentBorrowCount > maxBooksPerMember)` to `if (currentBorrowCount >= maxBooksPerMember)`. When the number of books currently borrowed = 3 (maximum limit), further borrowing must be rejected.

---

## BUG-02: Suspended member borrowing book receives "expired" error message instead of "suspended"

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-02 |
| **Related TC** | TC-15 |
| **Related REQ** | REQ-04 |
| **Severity** | Medium |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Login page is open, data has been reset (F5).

**Steps to Reproduce:**
1. Enter email `cu.le@email.com` (MEM004, Suspended status) and password `password123`.
2. Click **Login**.
3. Go to **Books** tab.
4. Find book BOOK001 (Lap trinh Flutter co ban, status "Available").
5. Click **Borrow** on BOOK001.

**Expected Result:**
System rejects borrowing. Displays error message containing **"suspended"** — e.g., "Member is currently suspended. Cannot borrow books." (Per SRS REQ-04: error message must describe the **correct reason** for rejection; suspended ≠ expired).

**Actual Result:**
System rejects borrowing but displays the message **"Member has expired. Cannot borrow books."** instead of "suspended". Users cannot distinguish whether the actual reason for rejection is suspension or expiration.

**Impact:**
Suspended members receive incorrect error messages, causing confusion about the reason for rejection. Violates SRS REQ-04 requirement that error messages must describe the correct reason. Librarians and members cannot determine the actual account status to take appropriate corrective action.

**Evidence:**
![BUG-02: "Expired" message when MEM004 borrows a book](evidences/BUG-02.png)

**Suggested Fix:**
Review the error message handling logic in the borrow book function. Ensure that when member status is "Suspended", the error message must contain the word "suspended". The code may be combining both "Suspended" and "Expired" cases into the same message branch.

---

## BUG-03: Valid email rejected with "Invalid email" message when adding a new member

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-03 |
| **Related TC** | TC-22 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Logged in as Librarian (`librarian@library.com` / `admin123`). Data has been reset (F5).

**Steps to Reproduce:**
1. Go to **Members** tab.
2. Click **Add Member**.
3. Enter full name: `Nguyen Van Moi`.
4. Enter email: `nguyenvanmoi@email.com` (valid email — has `@` AND has `.` in domain).
5. Enter phone number: `0901234567`.
6. Click **Create** / **Save**.

**Expected Result:**
New member created successfully. Member appears in the list with status "Active". (Per SRS REQ-07: valid email must have `@` AND `.` in domain — `nguyenvanmoi@email.com` satisfies both conditions).

**Actual Result:**
System rejects creating new member. Displays error message "Invalid email" even though `nguyenvanmoi@email.com` is completely valid per SRS.

**Impact:**
Cannot add any new member with a valid email — **member management feature is completely unusable**. This is a critical bug that paralyzes the entire REQ-07 feature. Combined with BUG-04, the email validation logic is **completely inverted**: valid → rejected, invalid → accepted. Probable cause: email validation logic is inverted or uses an incorrect regular expression (regex).

**Evidence:**
![BUG-03: "Invalid email" message with a valid email](evidences/BUG-03.png)

**Suggested Fix:**
Review the email validation logic in the add member form. The validation expression may be incorrect or inverted — accepting invalid emails (missing domain dot) while rejecting valid ones. Fix the regex/validation logic to ensure: an email with `@` AND `.` in the domain portion is considered valid.

---

## BUG-04: Invalid email `user@domain` (missing dot in domain) accepted when adding a member

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-04 |
| **Related TC** | TC-23 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Logged in as Librarian. Data has been reset (F5).

**Steps to Reproduce:**
1. Go to **Members** tab.
2. Click **Add Member**.
3. Enter full name: `Test User`.
4. Enter email: `user@domain` (has `@` but **no `.`** in the domain portion).
5. Enter phone number: `0901234567`.
6. Click **Create** / **Save**.

**Expected Result:**
System **rejects** creating member. Displays invalid email error message. (Per SRS REQ-07: email must be valid — have `@` **AND** have `.` in domain. Email `user@domain` is INVALID because it lacks `.` in domain).

**Actual Result:**
System **accepts** email `user@domain`, creates new member successfully. No error message displayed.

**Impact:**
Allows creating members with invalid emails, violating SRS REQ-07 and BR-08 validation rules. Member data is incorrect — invalid emails may cause errors when sending notifications or contacting members. Combined with BUG-03, the email validation logic is **completely inverted**: valid → rejected, invalid → accepted.

**Evidence:**
![BUG-04: Member created successfully with email user@domain](evidences/BUG-04.png)

**Suggested Fix:**
Fix the email validation logic — invert the check condition. Ensure email `user@domain` (missing `.` in domain) is rejected, and email `nguyenvanmoi@email.com` (with both `@` and `.` in domain) is accepted. May need to rewrite the email validation regex entirely.

---

## BUG-05: Duplicate email displays incorrect message "Invalid email" instead of "Email already exists"

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-05 |
| **Related TC** | TC-24 |
| **Related REQ** | REQ-07 |
| **Severity** | Medium |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Logged in as Librarian. Data has been reset (F5).

**Steps to Reproduce:**
1. Go to **Members** tab.
2. Click **Add Member**.
3. Enter full name: `Duplicate Email`.
4. Enter email: `ba.nguyen@email.com` (email already exists in the system — MEM002).
5. Enter phone number: `0901234567`.
6. Click **Create** / **Save**.

**Expected Result:**
System rejects creating member. Displays error message **"Email already exists"** (or similar, describing the correct reason for rejection as duplicate email). (Per SRS REQ-07: "Do not allow creating duplicate email → error message").

**Actual Result:**
System rejects creating member but displays error message **"Invalid email"** instead of "Email already exists". The rejection reason is correct (duplicate email) but the message is wrong (shows invalid format reason).

**Impact:**
Users (Librarians) receive incorrect messages — they do not know the actual reason is that the email already exists. They may misunderstand that the email format is wrong and try entering a different email instead of understanding they need to use a new email address. Causes confusion and wasted time.

**Evidence:**
![BUG-05: "Invalid email" message when entering an existing email](evidences/BUG-05.png)

**Suggested Fix:**
Separate the two check conditions: (1) Check email format validity → message "Invalid email", (2) Check email already exists → message "Email already exists". Ensure duplicate email check is performed and returns an appropriate message before the format validation.

---

## BUG-06: Member can view other members' borrow records — violates access control SRS REQ-08

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-06 |
| **Related TC** | TC-27 |
| **Related REQ** | REQ-08 |
| **Severity** | High |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Logged in with member account MEM002. Data has been reset (F5).

**Steps to Reproduce:**
1. Enter email `ba.nguyen@email.com` (MEM002) and password `password123`.
2. Click **Login**.
3. Go to **Borrow / Return** tab.
4. Default tab shows only MEM002's records (BR001, BR004) — correct.
5. In the borrow record lookup section, enter member ID **MEM003**.
6. Observe results — see records BR002 and BR005 of MEM003.
7. Enter member ID **MEM006**.
8. Observe results — see record BR003 of MEM006.

**Expected Result:**
MEM002 **cannot view** borrow records of MEM003 or MEM006. Per SRS REQ-08: "Members can only view **their own** borrow records. **Must NOT view other members' borrow records.**"

**Actual Result:**
Default tab displays correctly (only MEM002's records). However, when entering another member's ID (MEM003/MEM006), MEM002 can view their full borrow records. There is no access restriction on the lookup function.

**Impact:**
Serious violation of borrow record privacy rules (BR-07 and SRS REQ-08). Member A can view the entire borrow/return history of Member B, including book information, borrow dates, and status — this is a personal data leak. This is a high-impact security vulnerability in a production system.

**Evidence:**
![BUG-06: MEM002 views MEM003's borrow records](evidences/BUG-06_MEM003.png)

![BUG-06: MEM002 views MEM006's borrow records](evidences/BUG-06_MEM006.png)

**Suggested Fix:**
Add access control check in the borrow record lookup function: when the logged-in user is a "Member", only allow lookup with their own member ID. If a different member ID is entered → deny access or automatically redirect to their own records. Only "Librarian" should have the right to look up any member's records.

---

## BUG-07: Filtering books by genre "technology" (lowercase) returns 0 results — filter is case-sensitive

| Attribute | Details |
|-----------|---------|
| **Bug ID** | BUG-07 |
| **Related TC** | TC-28 |
| **Related REQ** | REQ-03 |
| **Severity** | Medium |
| **Detected By** | Team 11 |
| **Detection Date** | 06/06/2026 |
| **Status** | Open |

**Precondition:**
Logged in, on **Books** tab. Data has been reset (F5).

**Steps to Reproduce:**
1. Go to **Books** tab.
2. Filter by genre **Technology** (capitalized) → displays 8 books — correct.
3. Clear filter → Filter by genre **technology** (lowercase).
4. Observe result list.

**Expected Result:**
Displays **8 books** in the Technology genre — identical to results when filtering with "Technology" (capitalized). (Per SRS REQ-03: "Search is **case-insensitive**"). Rule BR-10 also requires: "Search must be case-insensitive".

**Actual Result:**
Displays message **"No books found"** — 0 results. The filter only works correctly when entering "Technology" (matching the exact case as in the data). The filter is case-sensitive.

**Impact:**
Users entering a genre in lowercase (common in real-world usage) will not find books even though the genre exists. Violates the case-insensitive requirement of SRS REQ-03 and BR-10. Causes poor user experience — users may think the library has no books in that genre.

**Evidence:**
![BUG-07: "No books found" when filtering "technology" in lowercase](evidences/BUG-07.png)

**Suggested Fix:**
Fix the genre filter logic: compare the filter value with the book genre after converting both to the same case (lowercase or uppercase) before comparing. For example: `filter.toLowerCase() === book.genre.toLowerCase()`. Ensure the filter operates case-insensitively as required by SRS.
