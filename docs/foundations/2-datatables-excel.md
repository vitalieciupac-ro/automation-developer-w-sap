# 2. DataTables & Excel

A **DataTable** is an in-memory spreadsheet: rows and typed columns. It's how UiPath holds tabular data between reading it from Excel and writing it somewhere else.

!!! abstract "What you'll be able to do"
    - Explain the DataTable structure (rows, columns, DataRow) and when to use it.
    - Read, filter, sort, and write tabular data with DataTable activities.
    - Use the modern Excel activities to read and write workbooks.

## The mental model

Columns are your **fields**, rows are your **records**, and one **DataRow** is a single record. You read a sheet into a DataTable, work with it in memory, then write it back out.

## Activity quick reference

| Activity | Use it to… |
|----------|------------|
| **Use Excel File** | Open a workbook once as a scope for the activities inside |
| **Read Range** | Load a sheet/range into a DataTable (turn on *Add Headers*) |
| **For Each Row in Data Table** | Loop over records; access with `currentRow("Column")` |
| **Filter Data Table** | Keep or remove rows matching a condition |
| **Sort Data Table** | Order rows by a column |
| **Lookup Data Table** | Fetch one value from a matching row (like VLOOKUP) |
| **Write Range** | Write a DataTable to a sheet |
| **Output Data Table** | Turn a table into a string (handy for logging) |

## Modern vs classic Excel

!!! info "Which activities to use"
    **Workbook** (System) activities read the file directly and don't need Excel installed, fast, but limited. **Excel** (modern, App Integration) activities automate the Excel application, richer, needs Excel. For this course we use the **modern Excel activities** inside a **Use Excel File** scope.

!!! autopilot "Build it yourself, then scaffold the workflow with Autopilot"
    Build the exercise yourself first so you understand each activity. Then, to see how Autopilot compares, **how:** open the **Autopilot** panel in Studio and type the task in plain language, for example *"read the Sales sheet from sales_report.xlsx, keep only EMEA rows, and total the Amount column"*. Autopilot proposes a sequence of activities; review the preview and insert it, then set the file, the ranges, and the column types yourself. It can get details wrong, so check every activity.

---

## Try it: Regional Sales Summary

!!! example "Scenario"
    Finance needs a quick regional summary from the monthly sales export. For a chosen region, produce the total sales amount and write a one-line summary back to the workbook.

**Packages:** `UiPath.Excel.Activities` (plus the default `UiPath.System.Activities`).

**What to produce**

- A process that reads `sales_report.xlsx`, filters to a target region, sums the `Amount` column, and writes the region and total to a new **Summary** sheet.
- A log line: `EMEA total: 12345.67`.

**You are given**

- `sales_report.xlsx` with a **Sales** sheet containing `Region, Product, Amount`.
- A target region, e.g. `targetRegion = "EMEA"`.

**Hints**

- Use **Use Excel File** as the scope; **Read Range** with *Add Headers* on.
- `row("Amount")` is an Object, `Convert.ToDouble(...)` before adding.
- **Filter Data Table** can keep only rows where `Region = targetRegion`.

??? success "Solution"
    Create `Ex2_SalesSummary`. Variables: `filePath` (String = the path to `sales_report.xlsx`), `sheetName` (String = `"Sales"`), `dtSales` (DataTable), `dtFiltered` (DataTable), `total` (Double), `targetRegion` (String = `"EMEA"`).

    1. Add **Use Excel File** pointing at `filePath`.
    2. Inside, add **Read Range** on the `sheetName` sheet → output `dtSales` (*Add Headers* checked).
    3. Add **Filter Data Table**: input `dtSales`, keep rows where `Region` equals `targetRegion`, output `dtFiltered`.
    4. **Assign** `total = 0`. Add **For Each Row in Data Table** over `dtFiltered`.
    5. Inside the loop, **Assign** `total = total + Convert.ToDouble(currentRow("Amount"))`.
    6. After the loop, add **Write Cell** to a **Summary** sheet: `A1 = targetRegion`, `B1 = total`.
    7. Add **Log Message** (Info): `$"{targetRegion} total: {total}"`.
    8. Run, then open the workbook to confirm the Summary sheet holds the region and total.

    **Expected result:** a new **Summary** sheet shows the region and its correct total; the log line matches the written total.

[Next: Reliable UI Automation](3-ui-automation.md){ .md-button .md-button--primary }
