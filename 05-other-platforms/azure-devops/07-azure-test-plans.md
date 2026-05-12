# Azure Test Plans

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Azure Test Plans is the quality assurance service in Azure DevOps. It provides structured manual testing - test plans, test suites, test cases with step-by-step instructions, exploratory testing sessions and the ability to run automated tests from within the testing interface. It is designed for teams that need formal QA workflows: documented test cases, pass/fail records, traceability from requirements to test results and audit trails for compliance.

Azure Test Plans is the most specialised and the most expensive of the five Azure DevOps services. It requires the **Basic + Test Plans** licence at $52/user/month. Teams doing only automated testing through pipelines do not need Test Plans - automated test results flow from Azure Pipelines without it. Test Plans is for teams with dedicated QA engineers who execute manual tests, manage test case libraries and maintain test coverage evidence for regulatory or compliance requirements.

---

## Table of Contents

1. [What Azure Test Plans provides](#what-azure-test-plans-provides)
2. [Test plans and test suites](#test-plans-and-test-suites)
3. [Test cases](#test-cases)
4. [Executing manual tests](#executing-manual-tests)
5. [Exploratory testing](#exploratory-testing)
6. [Running automated tests from Test Plans](#running-automated-tests-from-test-plans)
7. [Defect tracking from test execution](#defect-tracking-from-test-execution)
8. [Test reporting](#test-reporting)
9. [Traceability: requirements to tests](#traceability-requirements-to-tests)
10. [Configuration testing (cross-browser, cross-OS)](#configuration-testing-cross-browser-cross-os)
11. [Try It Yourself](#try-it-yourself)
12. [Common Mistakes](#common-mistakes)
13. [Summary](#summary)
14. [Sources](#sources)

---

## What Azure Test Plans provides

Azure Test Plans addresses three testing scenarios:

**Planned manual testing**: Structured test cases with defined steps and expected results, organised into test suites. A QA engineer works through each step and marks it pass or fail. The results are recorded for each test run.

**Exploratory testing**: Unstructured, free-form testing where a tester explores the application looking for issues without following a predefined script. The Test and Feedback browser extension captures screenshots, notes and bugs during exploration.

**Running automated tests**: Executing automated test cases (written in any framework) through the Test Plans interface for traceability. The test results appear in Test Plans alongside manual test results, connecting automated coverage to the formal test plan.

### Who needs Test Plans

Test Plans is designed for:
- Organisations with dedicated QA teams doing formal test execution
- Regulated industries (financial services, healthcare, aerospace, defence) requiring test documentation and audit trails
- Teams doing acceptance testing where clients or stakeholders must sign off on test results
- Teams with both automated and manual testing who need unified coverage reporting

Teams doing only automated testing via pipelines do not need Test Plans - pipeline test results are visible in the pipeline run and on pull requests without Test Plans. A development team with no dedicated QA function and no compliance requirements can skip Test Plans and save the $52/user/month cost.

---

## Test plans and test suites

### Test plan

A **test plan** is the top-level container for a testing effort. It typically maps to a sprint, a release or a specific feature being tested. A test plan has:

- A name and description
- A start and end date
- The team that owns it
- One or more test suites inside it

### Test suite types

A test plan contains **test suites**. There are three types:

**Static test suite**: A manually curated list of test cases. You add test cases to it explicitly. Use this for regression test suites (always test these things) or feature-specific suites.

**Requirement-based test suite**: Automatically shows all test cases linked to a specific Azure Boards work item (requirement). When a work item has linked test cases, a requirement-based suite shows them automatically. Use this to see test coverage per requirement.

**Query-based test suite**: Shows test cases matching an Azure Boards query. Dynamic - automatically updates as test cases are created or modified to match the query. Use this for "all regression tests" or "all tests tagged as smoke tests".

### Creating a test plan

1. Go to **Test Plans** in your Azure DevOps project
2. Click **+ New Test Plan**
3. Enter:
   - **Name**: descriptive name (e.g. "Sprint 23 Test Plan", "v2.3.0 Release Testing")
   - **Area**: the Azure Boards area this plan belongs to
   - **Iteration**: the sprint or iteration this plan covers
   - **Start and End dates**
4. Click **Create**

### Adding test suites

Within a test plan:
1. Click the **+** icon next to the plan name
2. Choose suite type (Static, Requirement-based or Query-based)
3. Configure and create

---

## Test cases

A **test case** is a work item (of type "Test Case") that defines what to test and how. Test cases have:

- **Title**: what is being tested
- **Steps**: the detailed procedure with expected results per step
- **Expected results**: what the tester should observe if the feature works correctly
- **Priority**: how critical this test is (1-4)
- **Automation status**: Manual, Planned for automation, or Automated
- **Links**: to requirements (User Stories, PBIs), bugs and other test cases

### Creating a test case

**From a test suite:**
1. Select a test suite in your test plan
2. Click **+ New Test Case**
3. Enter the title
4. Click the test case to open the full form

**From the work item form:**
Create a work item of type "Test Case" directly from **Boards** > **Work Items** > **New Work Item** > **Test Case**.

### Writing test steps

The steps grid defines the test procedure:

| Step | Action | Expected result |
|---|---|---|
| 1 | Navigate to the login page | Login page displays with username and password fields |
| 2 | Enter valid username: `testuser@example.com` | Username is accepted |
| 3 | Enter valid password: `TestPassword123!` | Password displays as masked characters |
| 4 | Click the "Sign In" button | User is redirected to the dashboard |
| 5 | Verify the dashboard loads | Dashboard displays user's name in the top right |

Steps support:
- Rich text with formatting
- Attachments (screenshots, design mockups, test data files)
- Shared step groups (reusable step sequences referenced by multiple test cases)

### Shared steps

Shared steps are reusable step sequences. If "Log in as a standard user" is a prerequisite for 50 test cases, create it as a shared step and reference it from each test case. When the login process changes, update the shared step once and all 50 test cases automatically reflect the change.

**Creating shared steps:**
1. In a test case, select the steps to share
2. Right-click > **Create shared steps**
3. Give the shared step group a name
4. Save

**Referencing shared steps:**
1. In a test case steps grid
2. Click **Insert shared steps**
3. Search and select the shared step group

### Parameterised test cases

Parameters allow one test case to run with multiple data sets. Create a parameterised test case to test the same flow with different inputs:

| Parameter | Value set 1 | Value set 2 | Value set 3 |
|---|---|---|---|
| @username | admin@example.com | user@example.com | guest@example.com |
| @password | AdminPass123! | UserPass456! | GuestPass789! |
| @expected_role | Administrator | Standard User | Guest |

In the steps, reference parameters as `@username`, `@password`, `@expected_role`. When running, the test runner iterates through each value set, creating a separate test result per iteration.

---

## Executing manual tests

### Starting a test run

1. In a test plan, select a test suite
2. Select the test cases to run (or select all)
3. Click **Run** > **Run for web application** (opens the test runner in the browser)

Or for desktop application testing:
- **Run** > **Run with options** > select the runner type

### The test runner

The test runner opens as a sidebar overlay on top of the application being tested. The tester can see the test steps in the runner while interacting with the actual application.

For each step, the tester marks:
- **Pass** (tick/checkmark) - the step passed
- **Fail** (cross) - the step failed
- **Blocked** - cannot complete this step (environment issue, dependency not ready)
- Add a comment or screenshot

The runner automatically captures the current URL and browser information. The tester can manually add screenshots using the camera icon.

### Passing and failing a test

When all steps are marked, mark the overall test as **Pass**, **Fail** or **Blocked**. This creates a test result record linked to the test case with:
- The outcome
- The tester's identity
- The date and time
- Comments and screenshots for failed steps
- The build number being tested (if specified)

### Specifying the build being tested

Before running tests, specify which build is under test. This links the test results to a specific pipeline build number, enabling coverage tracking by build:

1. In the test plan, click **Run settings**
2. Select the pipeline and build number being tested
3. Test results from this run are associated with that build

---

## Exploratory testing

Exploratory testing is unstructured - the tester explores the application without a predefined script, looking for unexpected issues and edge cases.

### The Test and Feedback extension

Azure Test Plans' exploratory testing relies on the **Test and Feedback** browser extension for Chrome and Edge:

1. Install from the [Chrome Web Store](https://chrome.google.com/webstore/detail/test-feedback/gnldpbnocfnlkkicnaplmkaphfdnlplb) or Edge Add-ons
2. Configure it with your Azure DevOps organisation URL
3. Select your project and team

### Starting an exploratory session

1. Click the extension icon in the browser toolbar
2. Click **Start session**
3. Select the Azure DevOps project and optionally link to a test plan
4. Begin testing the application

### During an exploratory session

The extension captures:
- **Screenshots**: click the camera icon to screenshot the current state
- **Screen recording**: record video of your testing session
- **Notes**: add text observations as you test
- **Bugs**: file a bug directly from the extension - it captures the URL, screenshots, browser info and your notes automatically
- **Test cases**: create a formal test case from what you just discovered

All captures are timestamped and linked to the exploratory session.

### Ending a session

Click **Stop session**. A summary appears showing everything captured. Review and submit the session. The session record appears in Test Plans with all captured bugs and notes.

---

## Running automated tests from Test Plans

Azure Test Plans can trigger automated tests associated with test cases, allowing automated test results to appear alongside manual test results for unified coverage tracking.

### Associating automated tests with test cases

1. Open a test case
2. Click the **Associated Automation** tab
3. Select the test project and the automated test name (the test class/method)
4. Save

The automated test must be in a format Azure DevOps can discover: MSTest, xUnit, NUnit, JUnit or other supported frameworks.

### Running associated automated tests

1. In a test plan, select test cases with associated automation
2. Click **Run** > **Run with options** > select **Automated tests using Release Stage** or a specific pipeline
3. Azure DevOps triggers the selected pipeline and filters test execution to the associated tests
4. Results flow back to Test Plans and appear next to the test cases

This creates traceability: a test case (the requirement for what to test) is linked to an automated test (the implementation of that test) and the results of each run are recorded against the test case.

---

## Defect tracking from test execution

A core workflow in Azure Test Plans is filing bugs directly from a failed test case execution.

### Filing a bug from a test run

1. During a test run, when a step fails, click **File a bug**
2. Azure DevOps pre-populates the bug with:
   - The test case title
   - The failed step and its expected result
   - Screenshots captured during the session
   - The system information (browser, OS)
   - The build being tested
   - A link back to the test case
3. Add more details and save

The bug is created as an Azure Boards work item linked to the test case. The test case shows the open bug count. When the bug is fixed and the test passes, the bug is closed and the test case shows as resolved.

### Linking existing bugs

During a test run, you can link an existing bug rather than creating a new one. This is useful when a failed step is a known issue already being tracked.

---

## Test reporting

### Test results and history

Each test case maintains a history of all runs: who ran it, when, what build was being tested and the outcome. This history is visible on the test case and in reports.

### Progress report

The **Progress report** (in the test plan's **Charts** or **Reports** tab) shows:
- Total test cases in the plan
- Outcomes breakdown: passed, failed, blocked, not run
- Progress over time (trend chart)
- Results by tester

### Test results dashboard widgets

Add test plan widgets to Azure DevOps dashboards:
- **Test Results Trend**: pass/fail trend over builds
- **Chart for Test Plans**: pie/bar chart of outcomes
- **Requirements Quality**: test coverage per requirement

---

## Traceability: requirements to tests

One of the key compliance use cases for Azure Test Plans is demonstrating that every requirement has been tested. This is achieved through the link between Azure Boards work items and test cases.

### Linking test cases to requirements

**Option 1 - From the test case:**
1. Open a test case
2. Click **Links** tab
3. **+ Add link** > **Tested by** (or **Tests**)
4. Search for and link the User Story or Requirement work item

**Option 2 - From the work item:**
1. Open a User Story or Requirement
2. Click **Links** tab
3. **+ Add link** > **Tested by**
4. Link the test case

**Option 3 - From a requirement-based test suite:**
When a test suite is requirement-based, all test cases in that suite are automatically linked to the parent requirement.

### Viewing coverage

Azure DevOps can show you which requirements have test cases and what their test status is. From the **Boards** view, columns can be added showing test status per work item. This gives the team a live view: "This User Story has 3 test cases, all of which passed in the last test run."

---

## Configuration testing (cross-browser, cross-OS)

**Configurations** allow the same test cases to be run against multiple environments - different browsers, operating systems or other variables - with separate pass/fail results per configuration.

### Creating configurations

1. Go to **Test Plans** > **Configurations**
2. Click **Add configuration**
3. Define configuration variables (e.g. `Browser` with values `Chrome`, `Firefox`, `Edge`)
4. Create named configurations (e.g. "Windows Chrome", "macOS Safari", "Linux Firefox")

### Assigning configurations to test suites

1. In a test suite, click **Configuration**
2. Select which configurations apply to this suite

The test suite now shows the test case × configuration combinations. Each combination has its own pass/fail record.

This enables a QA team to track: "Login test passed on Windows/Chrome and Windows/Firefox, but failed on macOS/Safari."

---

## Try It Yourself

> [!NOTE]
> Azure Test Plans requires the **Basic + Test Plans** licence ($52/user/month). If you are evaluating the features without purchasing, Microsoft offers a free 30-day trial of Basic + Test Plans for new users.

**Exercise 1 - Create a test plan**

1. Go to **Test Plans** in your Azure DevOps project
2. Click **+ New Test Plan**
3. Name it "Practice Test Plan"
4. Set dates for the next two weeks
5. Click **Create**

**Exercise 2 - Create test cases**

1. In your test plan, click **+ New Test Case**
2. Title: "Verify homepage loads"
3. Add steps:
   - Step 1: Open the browser and navigate to the homepage | Expected: Homepage loads within 3 seconds
   - Step 2: Check the page title | Expected: Title reads "Welcome to My App"
   - Step 3: Check the main navigation is visible | Expected: Navigation bar displays with correct menu items
4. Save

**Exercise 3 - Run a test**

1. Select your test case in the test suite
2. Click **Run** > **Run for web application**
3. The test runner opens
4. Work through each step and mark them Pass or Fail
5. At the end, mark the overall test as Passed
6. View the result in the test case history

**Exercise 4 - Install the Test and Feedback extension**

1. Install [Test and Feedback](https://chrome.google.com/webstore/detail/test-feedback/gnldpbnocfnlkkicnaplmkaphfdnlplb) for Chrome
2. Configure it with your Azure DevOps organisation
3. Start an exploratory session
4. Browse to any website and capture a screenshot
5. File a practice bug
6. End the session and view the captured content

---

## Common Mistakes

**Purchasing Test Plans licences for developers who only write automated tests**

If a developer writes automated tests that run in pipelines, they do not need a Test Plans licence. The licence is required for users who execute tests through the Test Plans interface - typically dedicated QA engineers. Developers can see test results in pipelines without Test Plans access.

**Creating test cases without clear expected results**

A test case without expected results is useless to the tester. "Click the button" is not a useful step. "Click the Submit button" with expected result "The form submits and the success page appears" gives the tester clear pass/fail criteria.

**Not linking test cases to requirements**

The traceability from requirement to test to result is the core compliance value of Test Plans. If test cases are not linked to requirements, you cannot demonstrate test coverage per requirement. Set up requirement-based test suites from the start.

**Running all tests as one big suite rather than organising by area**

One enormous test suite becomes unmanageable. Create suites per feature area, per requirement or per sprint. Teams can then focus runs on the relevant area and track coverage more granularly.

**Not specifying the build being tested**

Without specifying the build, test results are not linked to a specific version of the code. You cannot answer "Did we test build 142?". Always select the build before running a test session.

---

## Summary

Azure Test Plans provides structured manual testing, exploratory testing and automated test association. It requires the Basic + Test Plans licence ($52/user/month) and is most valuable for teams with dedicated QA engineers, compliance requirements or formal release acceptance criteria.

Test plans contain test suites (static, requirement-based or query-based) which contain test cases. Test cases define steps with expected results and can be parameterised for data-driven testing. Shared steps reduce maintenance for common prerequisites.

Executing tests through the test runner captures pass/fail per step, screenshots and bug links. Bugs filed during test runs are pre-populated with context and linked back to the failing test case.

Exploratory testing uses the Test and Feedback browser extension to capture screenshots, notes and bugs during unstructured testing sessions.

Traceability from requirements (Azure Boards work items) to test cases to test results is the compliance backbone of Test Plans. Configurations enable the same test cases to be tracked across different browsers, operating systems and other environmental variables.

---

## Sources

- [Microsoft Learn: Azure Test Plans overview](https://learn.microsoft.com/azure/devops/test/overview)
- [Microsoft Learn: Create a test plan](https://learn.microsoft.com/azure/devops/test/create-a-test-plan)
- [Microsoft Learn: Create test cases](https://learn.microsoft.com/azure/devops/test/create-test-cases)
- [Microsoft Learn: Run manual tests](https://learn.microsoft.com/azure/devops/test/run-manual-tests)
- [Microsoft Learn: Exploratory testing](https://learn.microsoft.com/azure/devops/test/explore-workitems-exploratory-testing)
- [Microsoft Learn: Test and Feedback extension](https://learn.microsoft.com/azure/devops/test/perform-exploratory-tests)
- [Microsoft Learn: Associate automated tests](https://learn.microsoft.com/azure/devops/test/associate-automated-test-with-test-case)
- [Microsoft Learn: Configurations](https://learn.microsoft.com/azure/devops/test/test-different-configurations)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
