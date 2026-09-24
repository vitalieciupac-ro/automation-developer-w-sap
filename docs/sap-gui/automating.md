# Automating SAP GUI

With scripting enabled and the **SAP Testing app** connection created, you'll build the automation as **three small workflows** and invoke them from a `Main`. Keeping login, navigation, and data entry apart is the modular approach from Module 6, applied to SAP: one workflow, one job.

**Packages:** `UiPath.UIAutomation.Activities` and `UiPath.GSuite.Activities` (Google Workspace), plus the default `UiPath.System.Activities`.

!!! abstract "What you'll build"
    - **Workflow 1 (`OpenSAPAndLogin`)**: open SAP GUI and log in.
    - **Workflow 2 (`NavigateToTransaction`)**: navigate to a transaction.
    - **Workflow 3 (`TypeIntoAField`)**: type a value into a field.
    - A `Main` that reads a **Google Sheet** of sales orders, drives SAP for each one, and writes a **status** back to the sheet.

## Workflow 1: OpenSAPAndLogin

!!! example "Build it together"
    Open the **SAP Testing app** connection from SAP Logon, enter the credentials, log in, and confirm you land on **SAP Easy Access**.

??? success "Steps"
    Create `OpenSAPAndLogin.xaml` with in-arguments `in_Client`, `in_User`, `in_Password`, `in_SystemID` so it can be called with different inputs.

    1. Add **Use Application/Browser** (or **Open Application**) and open `saplogon.exe`, then open the **SAP Testing app** connection.
    2. **Attach** to the login screen.
    3. **Type Into** the **Client**, **User**, **Password**, and **Language** fields from the arguments.
    4. Send **Enter** (or click the green confirm button) to log in.
    5. Verify the landing screen using the **SAP page title**: confirm it reads *SAP Easy Access*.
    6. **Log Message** `"SAP login OK"`.

!!! note "Get the credentials from Orchestrator"
    Don't hardcode the user and password. In `Main`, fetch them from an Orchestrator **Credential asset** with **Get Credential** and pass them into `in_User` and `in_Password`. See [Store the SAP credentials in Orchestrator](../foundations/3-ui-automation.md) for why this is safer and what other options exist.

## Workflow 2: NavigateToTransaction

!!! example "Build it together"
    From SAP Easy Access, navigate to a transaction using the **Call Transaction** activity.

??? success "Steps"
    Create `NavigateToTransaction.xaml` with in-argument `in_TCode`.

    1. Add the **Call Transaction** activity and set the transaction code from `in_TCode` (for example `VA03` to display a sales order). It runs in the current SAP GUI window.
        - Alternative: **Type Into** the command field with `"/n" + in_TCode` and send **Enter**.
    2. Add **Check App State** for the transaction's initial screen so you wait for it to open.
    3. Confirm arrival with the **SAP page title** or the **status bar** message.

    Prefer buttons over hotkeys where you can. If you must use a hotkey, give the activity a **specific selector** rather than sending it to the whole screen.

## Workflow 3: TypeIntoAField

!!! example "Build it together"
    On the transaction screen, type a value (passed as an argument) into a field, for example an order number or a search field.

??? success "Steps"
    Create `TypeIntoAField.xaml` with in-argument `in_Value`.

    1. Add **Type Into** targeting the SAP field. Capture the field into the **Object Repository** so it is reusable.
    2. Prefer **Simulate Type** for SAP fields; it works at the API level and is reliable.
    3. If needed, send **Enter** and confirm the result via the **status bar**.

## Bring it together in Main

`Main` orchestrates the whole process: it reads the work list from a **Google Sheet**, logs in to SAP once, processes each sales order, and writes a status back to the sheet.

The sheet has two columns: **Sales order** and **Status**.

!!! note "Google Sheets connection"
    The Google activities need a **Google Sheets connection**. Sign in to your Google account when Studio prompts, and make sure the sheet is shared with that account.

1. Add **Use Google Spreadsheet** (from `UiPath.GSuite.Activities`) and point it at your sheet.
2. Inside it, add **Read Range** with *Has headers* on → output `dtOrders`, a DataTable with the **Sales order** and **Status** columns.
3. **Invoke `OpenSAPAndLogin`** once, before the loop, so you log in a single time. (Fetch the credentials from an Orchestrator Credential asset, as in Module 3.)
4. Add **For Each Row in Data Table** over `dtOrders` (item `currentRow`). Inside the loop:
    1. Read the order: `orderNo = currentRow("Sales order").ToString`.
    2. **Invoke `NavigateToTransaction`** (pass the transaction, for example `VA03`) and **Invoke `TypeIntoAField`** (pass `orderNo`) to process the order in SAP.
    3. Capture a result, for example read the SAP status message, or set `"Processed"` on success.
    4. Write it back into the row: **Assign** `currentRow("Status") = status`.
5. After the loop, add **Write Range** → write `dtOrders` back to the sheet (with headers) so the **Status** column is updated.

!!! tip "Write each status as you go"
    Writing the whole table once at the end is simplest, but if the process stops midway you lose the statuses computed so far. To be safe, write each status immediately with **Write Cell** to the Status cell of the current row (for example `"B" & (index + 2)` when the header is in row 1), so progress is saved order by order.

Wrap the per-order SAP steps in a **Try Catch** (Module 4) so one bad order doesn't stop the run; on error, set that row's **Status** to the error and continue with the next order.

Just like Module 6, `Main` reads like a table of contents, and each SAP step is a small, separately testable piece.

[Next: Best practices](best-practices.md){ .md-button .md-button--primary }
