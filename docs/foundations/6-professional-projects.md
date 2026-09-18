# 6. Building Professional Projects

Everything so far made one workflow *work*. This module makes a project **maintainable**: the difference between code you can hand to a teammate and code only you understand.

!!! abstract "What you'll be able to do"
    - Split a monolithic workflow into reusable, argument-driven components.
    - Apply naming conventions, annotations, and project structure.
    - Run Workflow Analyzer and fix violations.
    - Understand publishing and where Orchestrator fits (conceptual).

## Make it modular

**Invoke Workflow File** runs one workflow from another; **arguments** (In / Out / In-Out) pass data across the boundary. Aim for *one workflow = one job*, and a `Main` that reads like a table of contents.

## Conventions that pay off

- Name workflows `Verb_Noun` (`Extract_InvoiceData`), not `Sequence3`.
- Prefix arguments `in_` / `out_` / `io_` so direction is obvious.
- **Annotate** complex activities so a reviewer understands intent without opening every file.

## Workflow Analyzer

A built-in linter with rules for naming, empty catches, hardcoded values, and more. Run it, read a violation, fix it, re-run to green.

!!! tip "Think of it as a free code review"
    Workflow Analyzer catches the obvious issues *before* a human reviewer sees your project, so the human review is about logic, not lint.

## Publishing & Orchestrator

Publishing packages the project (a `.nupkg`) to a **feed**: Orchestrator or a local/custom feed, so a robot can run it.

!!! info "Where Orchestrator fits"
    Orchestrator stores packages and schedules, triggers, and monitors robots; it also holds shared **Assets** and **Queues**. You'll go deeper on those, plus REFramework and unattended robots, in later Developer Associate courses. Today, just place them on the map.

---

## Try it: Make It Professional

!!! example "Scenario"
    Your invoice automation works but lives in one long `Main`. Refactor it into reusable pieces, pass the built-in code review, and publish it so a robot could run it.

**Packages:** the same as Module 5 (`UiPath.Mail.Activities`, `UiPath.PDF.Activities`), since you're refactoring that project. Invoke Workflow File is in the default `UiPath.System.Activities`.

**What to produce**

- A refactored project where the PDF-extraction logic is its own workflow invoked from `Main` via arguments.
- A clean **Workflow Analyzer** run (no violations) and a published package.

**You are given**

- Your Exercise 5 project (or the provided monolithic version).
- Naming convention: workflows `Verb_Noun`; arguments prefixed `in_` / `out_`.

**Hints**

- **Invoke Workflow File** runs a child workflow; arguments cross the boundary.
- One workflow = one job. `Main` should read like a table of contents.
- Workflow Analyzer flags naming and empty-catch issues, fix, then re-run.

??? success "Solution"
    Open the project. You'll extract the PDF-reading logic into `Extract_InvoiceData.xaml`.

    1. Create a new workflow `Extract_InvoiceData.xaml`. Add arguments `in_PdfPath (String)`, `out_InvoiceNo (String)`, `out_Total (String)`.
    2. Move the Read PDF Text + extraction activities into it; map the results to the out-arguments.
    3. In `Main`, replace that logic with **Invoke Workflow File** → `Extract_InvoiceData.xaml`, importing arguments (pass the PDF path in, read number/total out).
    4. Rename any generic sequences to `Verb_Noun` and add an **Annotation** on the invoke describing what it does.
    5. Ensure every **Catch** has a **Log Message** (the empty-catch rule).
    6. Run **Workflow Analyzer** (Design ribbon). Open each violation, fix it, and re-run until it reports no issues.
    7. Right-click the project → **Publish**. Choose a destination (Orchestrator feed if connected, otherwise a local custom feed) and publish.
    8. Confirm the success message and the generated `.nupkg` package name/version.

    **Expected result:** `Main` invokes `Extract_InvoiceData` with arguments and reads like a summary of the process; Workflow Analyzer reports no violations; a package is published to the chosen feed.

[Next: SAP GUI Automation](../sap-gui/index.md){ .md-button .md-button--primary }
