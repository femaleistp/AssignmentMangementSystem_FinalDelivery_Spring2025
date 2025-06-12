# Bug Reports

---

### BUG-2025-341: Notes Field Missing  
**Reported by:** QA Intern  
**Date:** 2025-06-02  

**Steps to Reproduce:**  
- Use POST `/api/assignment` to create an assignment with `"Notes": "Final project guidelines"`  
- Use GET `/api/assignment` to retrieve the assignment list  
- The Notes field is missing or empty in the response  

**Root Cause:**  
The `Assignment` constructor previously failed to assign the `notes` parameter to the `Notes` property.

**Fix:**  
This bug was resolved in a prior week. No code or test changes were made in Week 9.

**Test Added:**  
A regression test (`Constructor_WithNotes_ShouldAssignNotes`) was added in the earlier week alongside the fix to ensure the Notes field is correctly assigned.

---

### BUG-2025-349: IsOverdue Logic Incorrect  
**Reported by:** API Developer  
**Date:** 2025-06-02  

**Steps to Reproduce:**  
- Create an incomplete assignment with no due date — it incorrectly shows as overdue  
- Create a completed assignment with a past due date — it still shows as overdue  

**Root Cause:**  
The `IsOverdue()` method did not check for a null due date or completion status.

**Fix:**  
The logic was updated to return `true` only when:  
- A due date exists  
- The assignment is not completed  
- The due date is in the past

**Test Added:**  
The test `IsOverdue_ShouldReturnFalse_WhenAssignmentIsCompleted` confirms that completed assignments are not flagged as overdue. Additional coverage ensures the method handles null due dates correctly.

---

### BUG-2025-350: ConsoleUI Allows Empty Input
**Reported by:** QA Intern  
**Date:** 2025-06-11  

**Steps to Reproduce:**  
- Launch Console UI
- Select "Add Assignment"
- Leave title or description blank and press Enter
- Assignment fails or exception is caught

**Root Cause:**  
The ConsoleUI does not validate title or description before calling the constructor

**Fix:**  
Validation logic was added to ensure non-empty input before creating the assignment.

**Test Added:**  
Not applicable (manual test only)

---

### BUG-2025-351: Duplicate Assignments Allowed
**Reported by:** Manual Tester
**Date:** 2025-06-11  

**Steps to Reproduce:**  
- Add an assignment with the title "Quiz 1"
- Add another assignment with title "Quiz 1"
- Both are added without conflict

**Root Cause:**  
AssignmentService does not check for an existing assignment with the same title.
**Fix:**  
A check was added in 'AssignmentService.AddAssignment()' to prevent duplicates by comparing titles (case-insensitive).

**Test Added:**  
AddAssignment_ShouldRejectDuplicateTitle in AssignmentServiceTests.cs verifies that duplicates are rejected.

---

### BUG-2025-352: Missing Logging When Marking Nonexistent Assignment Complete
**Reported by:** QA Reviewer
**Date:** 2025-06-11  

**Steps to Reproduce:**  
- Choose "Mark Complete" in ConsoleUI
- Enter a title that doesn't exist
- Message is shown, but no logging occurs

**Root Cause:**  
Missing logging inside the failure branch of MarkAssignmentComplete()

**Fix:**  
Added logging inside AssignmentService.MarkAssignmentComplete() when assignment is not found.
**Test Added:**  
MarkAssignmentComplete_ShoudlLog_WhenAssignmentNotFound verifes the logger records a message for missing titles.

---

### BUG-2025-353: AssignmentPriority Enum Not Validated in API
**Reported by:** API Tester  
**Date:** 2025-06-11  

**Steps to Reproduce:**  
- Send POST request with invalid priority (e.g., "Extreme")
- App throws unhandled exception

**Root Cause:**  
ConsoleUI directly passes user input to the Assignment constructor without validation

**Fix:**  
Validate input before creating the Assignment object; reject blank titles/descriptions

**Test Added:**  
Unit test for AddAssignment() with empty strings and manual test for UI validation

---

### BUG-2025-354: DI Registration for IAssignmentService Missing  
**Reported by:** Developer (Brittany)  
**Date:** 2025-06-11  

**Steps to Reproduce:**  
- Register `AssignmentService` as `services.AddSingleton<AssignmentService, AssignmentService>();`
- Inject `IAssignmentService` into `ConsoleUI`
- Run the console app  

**Root Cause:**  
`IAssignmentService` was not registered in the DI container. Only the concrete `AssignmentService` was, which caused a runtime `InvalidOperationException`.

**Fix:**  
Change registration to `services.AddSingleton<IAssignmentService, AssignmentService>();`  

**Test Added:**  
No automated test; manually verified by launching `ConsoleUI`

---

### BUG-2025-355: Mock Verification Failed in Overdue Logging Test  
**Reported by:** Developer (Brittany)  
**Date:** 2025-06-11  

**Steps to Reproduce:**  
- Run the unit test `CheckIfOverdue_ShouldLogResult_WhenAssignmentExists`  
- The test fails with `Moq.MockException`  
- Message shows:  
  `Expected invocation on the mock once, but was 0 times`

**Root Cause:**  
The test expected a specific log format, but the logging in `CheckIfOverdue()` was not implemented at the time.

**Fix:**  
Implemented `CheckIfOverdue(string title)` in `AssignmentService`.  
The method logs a message using `LogAssignmentAction()` when an assignment is found, and logs a fallback message if not.

**Test Added:**  
The unit test `CheckIfOverdue_ShouldLogResult_WhenAssignmentExists` was completed and passes.  
It confirms that the method logs the correct message format and returns the correct overdue status.

