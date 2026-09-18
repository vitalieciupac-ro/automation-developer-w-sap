# Automating SAP GUI

With scripting enabled and the **SAP Testing app** connection created, you'll build the automation as **three small workflows** and invoke them from a `Main`. Keeping login, navigation, and data entry apart is the modular approach from Module 6, applied to SAP: one workflow, one job.

**Packages:** `UiPath.UIAutomation.Activities` (plus the default `UiPath.System.Activities`).

!!! abstract "What you'll build"
    - **Workflow 1 (`OpenSAPAndLogin`)**: open SAP GUI and log in.
    - **Workflow 2 (`NavigateToTransaction`)**: navigate to a transaction.
    - **Workflow 3 (`TypeIntoAField`)**: type a value into a field.
    - A `Main` that invokes all three in order.

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

In `Main`, use **Invoke Workflow File** to run the three workflows in order, passing the arguments through:

1. Invoke `OpenSAPAndLogin` with the credentials and system ID.
2. Invoke `NavigateToTransaction` with the transaction code.
3. Invoke `TypeIntoAField` with the value.

Just like Module 6, `Main` now reads like a table of contents, and each SAP step is a small, separately testable piece.

[Next: Best practices](best-practices.md){ .md-button .md-button--primary }
