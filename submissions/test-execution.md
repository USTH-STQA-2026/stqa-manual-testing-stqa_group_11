# Test Execution — Test Results

> **Guide**: Run each TC on the system at https://stqa.rbc.vn and record the actual results.
> Verdict: **Pass** (correct result), **Fail** (wrong result → create bug report), **Blocked** (cannot execute due to another blocking issue), **Not Run** (not yet executed).

| Information | |
|---|---|
| **Team** | Team 11 |
| **Execution Date** | `<!-- DD/MM/YYYY -->` |
| **Browser** | Chrome `<!-- version -->` |
| **Operating System** | `<!-- Windows / macOS / Linux -->` |

---

## Detailed Results

| TC ID | Feature Group | Expected Result (summary) | Actual Result | Verdict | Evidence | Bug |
|-------|--------------|--------------------------|---------------|---------|----------|-----|
| TC-01 | Login | Login successful, AppBar shows name + Librarian role | Redirected to home page. AppBar displays 'Nguyen Thu Thu — Librarian' | Pass | | |
| TC-02 | Login | Message "Member not found" | Message "Member not found" displayed | Pass | | |
| TC-03 | Login | Message "Incorrect password" | Message "Incorrect password" displayed | Pass | | |
| TC-04 | Login | Message "Please enter email and password" | Message "Please enter email and password" displayed | Pass | | |
| TC-05 | View Books | Both roles can view all 20 books with complete info | Both roles can view all 20 books | Pass | | |
| TC-06 | View Books | BOOK001 changes to "Borrowed" after borrowing, real-time | Status "Borrowed" displayed immediately | Pass | | |
| TC-07 | Search | Shows BOOK001 when searching "Flutter" | BOOK001 shown when searching "Flutter" | Pass | | |
| TC-08 | Search | Shows BOOK001 + BOOK009 when searching "Nguyen Minh Duc" | BOOK001 + BOOK009 shown when searching "Nguyen Minh Duc" | Pass | | |
| TC-09 | Search | Empty list, message "No books found" | Message "No books found" shown with random keyword | Pass | | |
| TC-10 | Search | "flutter" and "FLUTTER" give same result as "Flutter" | Both show same result | Pass | | |
| TC-11 | Filter Books | Shows 8 Technology genre books | All 8 Technology books displayed | Pass | | |
| TC-12 | Borrow Book | Borrow BOOK004 successful for MEM002, new record created | Borrow successful | Pass | | |
| TC-13 | Borrow Book | Reject borrowing BOOK003 (borrowed), message book not available | No borrow button shown | Pass | | |
| TC-14 | Borrow Book | Reject borrowing BOOK007 (lost), message book not available | No borrow button shown | Pass | | |
| TC-15 | Borrow Book | Reject MEM004 (suspended), message contains "suspended" | Borrow rejected but message displays "expired" instead of "suspended" | **Fail** | | BUG-01 |
| TC-16 | Borrow Book | Reject MEM005 (expired), message contains "expired" | Borrow rejected, message displays "expired" | Pass | | |
| TC-17 | Borrow Book | Steps 1–3: borrow successful; Step 4: rejected, 3-book limit | Steps 1–3: borrow successful. Step 4: 4th book borrowed successfully, no rejection | **Fail** | | BUG-02 |
| TC-18 | Return Book | Return BOOK013 successful, book → "Available", no warning | Return successful, BOOK013 → "Available", no overdue warning | Pass | | |
| TC-19 | Return Book | Overdue warning shown when returning BOOK003 (BR001) | Return successful, BOOK003 → "Available", NO overdue warning displayed | **Fail** | | BUG-03 |
| TC-20 | Overdue | BR001 marked "Overdue" after Librarian clicks check | BR001 and BR003 both marked "Overdue" (correct: both have dueDate ≤ today) | Pass | | |
| TC-21 | Overdue | MEM002 sees BR001 overdue, cannot see other members' records | MEM002 sees BR001 "Overdue", does not see other members' records | Pass | | |
| TC-22 | Members | New member created successfully | Valid email `nguyenvanmoi@email.com` rejected with message "Invalid email" | **Fail** | | BUG-04 |
| TC-23 | Members | Error: invalid email (`user@domain` missing dot) | Invalid email `user@domain` accepted, new member MEM007 created successfully | **Fail** | | BUG-05 |
| TC-24 | Members | Error: email already exists | Rejected but wrong message "Invalid email" instead of "Email already exists" | **Fail** | | BUG-06 |
| TC-25 | Borrow Records | Librarian views all 5 records BR001–BR005 | All 5 records displayed: BR001, BR003 "Borrowing"; BR002, BR004, BR005 "Returned" | Pass | | |
| TC-26 | Borrow Records | MEM002 sees only BR001 + BR004 | Only BR001 and BR004 shown, other members' records not visible | Pass | | |
| TC-27 | Borrow Records | MEM002 cannot view records of MEM003/MEM006 | MEM002 can view records of MEM003 (BR002, BR005) and MEM006 (BR003) — violates REQ-08 | **Fail** | | BUG-07 |
| TC-28 | Filter Books | "technology" (lowercase) gives same result as "Technology" | Filtering "technology" (lowercase) returns "No books found" — 0 results | **Fail** | | BUG-08 |

> **📌 Additional Finding (outside main TCs)**: When logged in as Librarian, the "Books" tab shows **no Borrow button** on any book. The Librarian also **cannot create a borrow record** for any member from the "Borrow / Return" tab. Per SRS: Librarian must have permission to "borrow/return books for members" → This is **BUG-09**.

---

## Results Summary

| Metric | Value |
|--------|-------|
| Total test cases | 28 |
| Pass | 20 |
| Fail | 8 |
| Blocked | 0 |
| Not Run | 0 |
| **Pass Rate** | **71.4%** |

### Results by Feature Group

| Group | Total TC | Pass | Fail | Pass Rate |
|-------|---------|------|------|-----------|
| Login (REQ-01) | 4 | 4 | 0 | 100% |
| View Books (REQ-02) | 2 | 2 | 0 | 100% |
| Search & Filter (REQ-03) | 6 | 5 | 1 | 83.3% |
| Borrow Book (REQ-04) | 6 | 4 | 2 | 66.7% |
| Return Book (REQ-05) | 2 | 1 | 1 | 50% |
| Overdue (REQ-06) | 2 | 2 | 0 | 100% |
| Members (REQ-07) | 3 | 0 | 3 | 0% |
| Borrow Records (REQ-08) | 3 | 2 | 1 | 66.7% |
