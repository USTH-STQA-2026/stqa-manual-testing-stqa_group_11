# Test Summary — Test Summary Report

> **Instructions**: This is a **Quality Assurance** activity — you evaluate the overall quality of the software, not just list bugs.

---

## 1. Team Information

| Item | Information |
|------|-------------|
| **Team** | Team 11 |
| **Class** | 252ICT2012.11 |
| **Report Date** | 06/06/2026 |
| **System Under Test** | https://stqa.rbc.vn — v1.0 |

---

## 2. Results Overview

| Metric | Value |
|--------|---------|
| Total test cases | 28 |
| Pass | 20 |
| Fail (bug) | 7 |
| Fail (unimplemented feature — no bug report) | 1 (TC-19) |
| Blocked | 0 |
| Not Run | 0 |
| **Pass Rate** | **71.4%** |
| **Bugs Detected** | **7** |

### Results by Feature Group

| Feature Group | TC | Pass | Fail | Bug | Assessment |
|---------------|-----|------|------|-----|---------|
| Login (REQ-01) | 4 | 4 | 0 | 0 | ✅ Stable, all Pass |
| View Book List (REQ-02) | 2 | 2 | 0 | 0 | ✅ Display correct, real-time updates |
| Search & Filter (REQ-03) | 6 | 5 | 1 | BUG-07 | ⚠️ Search works correctly, genre filter is case-sensitive |
| Borrow Book (REQ-04) | 6 | 4 | 2 | BUG-01, BUG-02 | ❌ Critical bugs: borrow limit bypassed, incorrect error message |
| Return Book (REQ-05) | 2 | 1 | 1* | 0 | ⚠️ Return works correctly, but missing overdue warning (unimplemented) |
| Overdue Handling (REQ-06) | 2 | 2 | 0 | 0 | ✅ Overdue marking correct |
| Member Management (REQ-07) | 3 | 0 | 3 | BUG-03, BUG-04, BUG-05 | ❌ Feature completely broken: email validation logic inverted |
| Borrow Record Lookup (REQ-08) | 3 | 2 | 1 | BUG-06 | ❌ Security bug: member can view other members' records via lookup |

> *\* TC-19 Fail due to unimplemented overdue warning feature, not a code bug.*

### Bug Distribution by Severity

| Severity | Count | Bug IDs |
|----------|-------|---------|
| High | 4 | BUG-01, BUG-03, BUG-04, BUG-06 |
| Medium | 3 | BUG-02, BUG-05, BUG-07 |

> **Note**: BUG-03, BUG-04, BUG-05 share the same root cause (inverted email validation logic) but are reported separately because each bug manifests differently and affects users in different ways.

---

## 3. Test Design Techniques Applied

| Technique | Applied to which REQ? | Number of TCs using it | Explanation of application |
|----------|---------------------|---------------|------------------------|
| **Equivalence Partitioning (EP)** | REQ-01, REQ-02, REQ-03, REQ-04, REQ-05, REQ-06, REQ-07, REQ-08 | 22 | Divide input data into equivalence partitions, testing one representative value per partition. Example: REQ-01 divides email into 3 partitions (correct/wrong/empty); REQ-04 divides book status into 3 partitions (Available/Borrowed/Lost). |
| **Boundary Value Analysis (BVA)** | REQ-03, REQ-04, REQ-07 | 5 | Test at partition boundaries — where bugs are most likely. Example: REQ-04 tests borrow limit at the 3-book boundary (BVA detected BUG-01: off-by-one); REQ-07 tests email at the valid/invalid boundary (missing domain dot); REQ-03 tests genre filter at the uppercase/lowercase boundary. |
| **Decision Table** | REQ-04 | 1 table (18 rules → 6 unique TCs) | List all combinations of 3 conditions (Book Status × Member Status × Number of Books Borrowed) → 18 rules. After eliminating duplicate results, 6 TCs cover all unique outcomes. Decision Table ensures no condition combination is missed. |

### Why was this technique chosen for each REQ?

| REQ | Primary Technique | Reason |
|-----|-------------------|--------|
| REQ-01 (Login) | EP | Input has clear discrete groups: correct/wrong/empty email, correct/wrong/empty password |
| REQ-03 (Search & Filter) | EP + BVA | EP for results found/not found; BVA for case boundary (case-insensitive) |
| REQ-04 (Borrow Book) | EP + BVA + Decision Table | Multiple combined conditions → Decision Table needed; 3-book limit → BVA at boundary |
| REQ-07 (Member Management) | EP + BVA | EP for valid/duplicate email; BVA for valid/invalid email boundary (missing domain dot) |

---

## 4. Software Quality Analysis

### 4.1. Strengths

1. **Login (REQ-01)**: Stable — correctly validates email/password, displays appropriate error messages for each case (wrong email, wrong password, empty fields). Pass Rate: 100%.

2. **View Book List (REQ-02)**: Displays all 20 books with detailed information for both roles. Real-time status updates when borrowing/returning — no refresh needed. Pass Rate: 100%.

3. **Overdue Handling (REQ-06)**: Correctly marks overdue records when Librarian clicks "Check Overdue". Both BR001 and BR003 (dueDate ≤ today) are marked accurately. Pass Rate: 100%.

4. **Keyword Search (REQ-03)**: Search by book title and author works correctly, case-insensitive for text search (e.g., "flutter" = "Flutter" = "FLUTTER"). Bug only occurs with the genre filter.

5. **Basic Return Book Function (REQ-05)**: On-time return works well — book changes to "Available", record changes to "Returned" accurately.

6. **Basic Borrow Book Function (REQ-04)**: Borrowing for active members works, Available books → "Borrowed", creates records with correct 14-day due date. Unavailable book statuses (Borrowed, Lost) display correctly — no Borrow button shown.

### 4.2. Weaknesses

1. **Borrow limit bypassed (BUG-01)**: Members can borrow more than 3 books — violating core business rule BR-01. The off-by-one error (`>` instead of `>=`) makes the limit completely ineffective when a member is currently borrowing exactly 3 books.

2. **Member Management feature completely broken (REQ-07)**: Pass Rate **0%**. Email validation logic is inverted — valid emails rejected (BUG-03), invalid emails accepted (BUG-04). Cannot add any new member with a correctly formatted email → entire REQ-07 feature is unusable.

3. **Incorrect error message for Suspended members (BUG-02)**: System always displays "expired" for both Suspended and Expired members — users cannot distinguish the actual reason.

4. **Security bug — Data leak (BUG-06)**: Members can view other members' borrow records when looking up by member ID — violating REQ-08 and BR-07. This is a serious security bug, leaking personal information.

5. **Incorrect duplicate email message (BUG-05)**: Existing email shows "invalid" instead of "already exists" — causing user confusion.

6. **Case-sensitive genre filter (BUG-07)**: Filtering "technology" (lowercase) returns 0 results — violating the case-insensitive requirement of SRS REQ-03.

### 4.3. Unimplemented Features (not bugs)

1. **Overdue warning when returning books (REQ-05)**: SRS requires displaying a clear warning when returning overdue books, but the system does not display any warning — this feature has not been built.

2. **Librarian borrowing books for members (SRS Section 1, 4.1)**: SRS specifies that Librarians have the right to "borrow/return books for members", but the Books tab does not display a Borrow button when logged in as Librarian. TC-15/TC-16 had to adjust the execution method (logging in directly with the member account) to test the borrow rejection logic.

---

## 5. Bug Fix Priority Recommendations

> 💡 Priority criteria: Combine **severity** (technical seriousness) and **priority** (business urgency). High priority for: bugs violating core business rules, security bugs, bugs that paralyze entire features.

| Order | Bug | Severity | Priority Reason |
|-------|-----|----------|-----------------|
| **1** | BUG-01 | High | **Core business rule violation**: 3-book limit bypassed, members can borrow unlimited. Directly impacts library management workflow. Simple fix: change `>` to `>=`. |
| **2** | BUG-03 + BUG-04 | High | **Feature completely broken**: email validation logic inverted → cannot add new members. Two bugs share the same root cause, fixing once will resolve both. |
| **3** | BUG-06 | High | **Security bug**: personal data leak — members can view other members' borrow history via lookup. In a production system, this is a personal data security violation. |
| **4** | BUG-02 | Medium | **Incorrect message**: Suspended shows "expired". Causes confusion but does not affect the ability to reject borrowing. Should be fixed alongside the member status branching logic. |
| **5** | BUG-05 | Medium | **Incorrect message**: duplicate email shows "invalid" instead of "already exists". Does not affect functionality (still rejects correctly), but causes confusion. Fix alongside BUG-03/04. |
| **6** | BUG-07 | Medium | **Case-sensitive filter**: violates REQ-03 but only affects users typing in lowercase. Workaround: enter the exact case as in the data. Affects user experience more than business logic. |

### Proposed Bug Fix Roadmap

| Phase | Bugs to Fix | Estimated Time | Rationale |
|-------|------------|----------------|-----------|
| **Phase 1 — Critical** | BUG-01, BUG-03+04 | 1–2 days | Fix core business rule violations + restore member creation feature |
| **Phase 2 — Security + Messages** | BUG-06, BUG-02, BUG-05 | 2–3 days | Fix data leak + improve error messages |
| **Phase 3 — UX** | BUG-07 | 1 day | Fix case-sensitive filter |

### Additional Features to Implement (not bugs — but needed for SRS compliance)

| Feature | SRS Reference | Priority Level |
|---------|---------------|----------------|
| Overdue warning when returning books | REQ-05 | High — overdue management business rule |
| Librarian borrowing books for members | SRS Section 1, 4.1 | High — main business workflow |

---

## 6. Conclusion

The ABC Library Borrowing Management System is **not ready for release**. With a Pass Rate of **71.4%** and **7 bugs** (including **4 High-severity bugs**), the software has many serious issues directly affecting core business functionality:

- **Borrow limit bypassed** (BUG-01) — loss of control over book quantities
- **Cannot add new members** (BUG-03/04) — entire REQ-07 paralyzed
- **Personal data leak** (BUG-06) — serious security vulnerability

Additionally, **2 important features are not yet implemented**: overdue warning when returning books (REQ-05) and Librarian borrowing books for members (SRS Section 1).

**Recommendation**: All High-severity bugs (Phase 1 + 2) must be fixed before considering release. After fixes, **Regression Testing** is required — re-run all 28 TCs to ensure the fixes do not introduce new bugs.

---

## 7. Lessons Learned

1. **Test design techniques are effective in practice**: BVA detected BUG-01 (off-by-one at the 3-book limit boundary) and BUG-04 (valid/invalid email boundary). If only happy path testing was done, both bugs would be missed.

2. **Decision Table ensures condition combination coverage**: REQ-04 has 3 combined conditions → Decision Table ensures no case is missed. If only EP was used, combinations like "Available book + Suspended member" could be overlooked.

3. **Strong Test Oracle is needed to detect bugs**: BUG-02 was only detected because the expected result specified "must contain the word 'suspended'" (Strong Oracle). If the expected result was simply "reject borrowing" (Weak Oracle), the bug would pass because the system does reject — it just gives the wrong reason.

4. **Testing process needs flexibility**: When discovering that Librarians have no Borrow button (unimplemented feature), the team adjusted the execution method for TC-15/TC-16 by logging in directly with the member account, rather than skipping the test case.

5. **Bugs concentrate at boundaries and condition combinations**: 7/7 bugs were found at boundary or negative test cases, none at simple happy path. This confirms the importance of testing not only main flows but also edge cases and boundary values.

6. **Distinguishing bugs from unimplemented features**: During testing, it is important to clearly distinguish between code bugs (behavior differs from design) and features that have not been built. Accurate recording helps the development team prioritize fixing actual bugs first, rather than confusing them with implementing new features.

---

## 8. AI Usage Declaration (Optional)

> The team used AI tools to assist with the testing process, then independently cross-checked all results against the SRS.

| AI Tool | Used for which part | How you verified/edited the output |
|---------|-------------------|-----------------------------------|
| Claude (Anthropic) | Assisted with IDM analysis, suggested EP/BVA data ranges, reviewed bug report formatting | Independently cross-checked all expected results against SRS; personally executed all TCs on the live system; independently verified Pass/Fail results; edited bug reports to match actual observations |
