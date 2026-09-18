# 3. Reliable UI Automation in SAP S/4HANA

In this module you automate **SAP S/4HANA Cloud** in the browser, through the **SAP Fiori launchpad**.

UI automation fails for two reasons, and Fiori sharpens both because it is a **SAPUI5** app that loads asynchronously and often changes element IDs between sessions:

- The robot can't **find** the element. Fix: descriptors and stable selectors.
- The robot acts before the element is **ready**. Fix: synchronization.

!!! abstract "What you'll be able to do"
    - Log in to the SAP S/4HANA Fiori launchpad and confirm it loaded.
    - Read and build selectors for SAPUI5 elements using stable attributes, anchors, and wildcards.
    - Apply synchronization so steps wait for Fiori to finish loading instead of guessing.
    - Find and open a standard Fiori app, then extract information from it.
    - Reuse SAP screen elements with the Object Repository.

## Selectors for SAPUI5 elements

A selector identifies an element from its attributes. Fiori is built on **SAPUI5**, which often assigns IDs that regenerate between sessions, for example `id='__button23'` or `id='__xmlview0--userInput'`. Learn to read a selector and spot the fragile part, then target something stable instead: the visible **text**, the **aria-label**, the control **type**, or a nearby **anchor**.

| Technique | What / why | SAP example |
|-----------|------------|-------------|
| **Full vs partial** | A partial selector omits the top window (the scope supplies it) | Used inside **Use Application/Browser** on the launchpad |
| **Wildcards** | Absorb the volatile part of a UI5 id: `*` = many chars, `?` = one | `id='__button*'` |
| **Anchors** | Target an element by a stable neighbour (a label next to a field) | Anchor the input to its **"User"** label |
| **Dynamic selector** | Inject a variable to retarget the same selector | `aria-label='{{fieldLabel}}'` |

## Synchronization: wait for Fiori, don't guess

Fiori loads tiles, apps, and data asynchronously, so a fixed **Delay** is guessing. It is too short when the system is slow and wasteful when it is fast.

- **Check App State**: branch on whether an element appeared within a timeout. Put one between each navigation step, because Fiori round-trips to the server.
- **Wait for Ready**: on SAP web screens, prefer **Interactive** over **Complete**. SAPUI5 keeps making background requests, so *Complete* can wait far too long or time out.
- **Sensible timeouts**: long enough for a slow S/4HANA response, not so long you hide a real failure.

## Finding and opening apps

In S/4HANA Cloud you get to a task by opening a **Fiori app**. Two ways to find one:

- The **launchpad search** at the top of the shell: type part of an app's name and open it from the results.
- The **App Finder**, the catalog of every app assigned to you, reachable from the launchpad menu.

Both are ordinary browser interactions. The second exercise below searches for a standard app, opens it, and reads a value from it.

## Object Repository

Capture SAP screen elements once, **name** them (for example `FioriUserField`, `LaunchpadSearch`, `AppResultTile`), and reuse them across the project. When SAP is patched and the underlying IDs shift, you fix the descriptor in **one place** instead of hunting through every activity.

---

## Try it: Log in to SAP S/4HANA (Fiori)

!!! example "Scenario"
    Automate a resilient login to the **SAP S/4HANA Fiori launchpad** in the browser. It has to wait for the log-on page and for the launchpad home to load, rather than guessing, and survive minor changes to the SAPUI5 elements.

**Packages:** `UiPath.UIAutomation.Activities` (plus the default `UiPath.System.Activities`).

**What to produce**

- A process that opens the Fiori launchpad URL, waits for the log-on page, enters the user and password, clicks **Log On** only when it is present, and confirms the launchpad home appeared.
- The user and password fields captured in the **Object Repository** and reused.

**You are given**

- The S/4HANA Cloud launchpad URL, for example `https://my<NNNNNN>.s4hana.cloud.sap/ui`.
- Test credentials: `user` and `password` for your training client.

**Hints**

- Never "fix" timing with a fixed Delay, use **Check App State**.
- Set **Wait for Ready** to **Interactive** so SAPUI5 background loading doesn't stall your steps.
- If a field or button has a dynamic UI5 id, target it by **text** / **aria-label**, or wildcard the id with `*`.
- Capture the fields into the **Object Repository** to reuse and maintain them.

??? success "Solution"
    Create `Ex3_SAP_Login`. Work inside a **Use Application/Browser** scope so selectors are partial.

    1. Add **Use Application/Browser** and open the launchpad URL.
    2. Add **Check App State** targeting the **User** field (say, 20s timeout, S/4HANA can be slow to greet you). Put the login steps in the *Target appears* branch.
    3. Add **Get Credential** pointing at the Orchestrator Credential asset `SAP_Credential`; it outputs the username (String) and password (SecureString).
    4. Add **Type Into** the user field → the username from Get Credential. Capture it into the **Object Repository** as `FioriUserField`.
    5. Add **Type Into** the password field → the password from Get Credential. Capture as `FioriPassField`.
    6. Add a **Click** on the **Continue** button.
    7. Add a final **Check App State** for a launchpad home element (for example the user avatar or a known tile) to confirm success. **Log Message** `"SAP login OK"`, or `"SAP login failed"` on the else branch.
    8. Run twice; on the second run turn on **Slow Step** to watch the synchronization behave.

    **Expected result:** the automation reaches the Fiori launchpad home reliably across runs with no fixed Delay; the two fields appear under **Objects** and are reused by the Type Into activities.

### Store the SAP credentials in Orchestrator

Rather than typing the username and password into the workflow, keep them in an Orchestrator **Credential asset** and fetch them at runtime.

**Create the asset (in Orchestrator):**

1. Open your folder in Orchestrator and go to **Assets**.
2. Add a new asset, choose type **Credential**, name it `SAP_Credential`, enter the username and password, and save.

**Use it (in Studio):** add **Get Credential**, point it at `SAP_Credential`, and feed its username and password outputs into the Type Into activities instead of literals.

**Why this is safer:**

- The password is stored **encrypted** in Orchestrator and handed to the robot as a **SecureString**, so it never appears in the workflow, in source control, or in the logs.
- Access is governed by **folder roles and permissions**, so only authorized robots and people can read it.
- You can **rotate** the password in one place, without editing or republishing the automation.

**Other ways to store credentials:**

- **Credential Stores**: Orchestrator can delegate to an external secrets vault such as **CyberArk**, **Azure Key Vault**, **HashiCorp Vault**, **AWS Secrets Manager**, or **BeyondTrust**, so the secret lives in the enterprise vault rather than Orchestrator's own database.
- **Windows Credential Manager**: **Get Credential** can also read from the local Windows store, useful for an attended robot on a specific machine.
- **Avoid** hardcoding secrets in the workflow or a plaintext config file, which exposes them in source control and logs.

## Try it: Launch a standard app and extract information

!!! example "Scenario"
    With the launchpad open, **search for a standard Fiori app, open it, and extract a piece of information it shows**. We use **My Inbox**, the workflow inbox available in essentially every S/4HANA Cloud system, and read the number of open tasks. The point is the pattern, search, open, wait, extract, not the specific app.

**Packages:** `UiPath.UIAutomation.Activities` (plus the default `UiPath.System.Activities`).

**What to produce**

- A process that types an app name into the launchpad search, opens the app from the results, waits for it to load, extracts a value it displays (for example the open-task count), and logs it.

**You are given**

- A logged-in Fiori launchpad (reuse the login from the previous exercise, or run it first).
- A standard app name to search for. Default: `My Inbox`.

**Hints**

- Use the launchpad **search** field, type the app name, then **Check App State** for the results before you open the app.
- **Check App State** for the app's content area before extracting; Fiori apps load asynchronously and the value is not there the instant the tile opens.
- Extract a displayed value with **Get Text**, targeting it by **text**, **aria-label**, or an **anchor**, not a raw UI5 id.
- If `My Inbox` is not assigned to your user, substitute any standard app you do have (**App Finder** is always present). The steps are identical.

??? success "Solution"
    Create `Ex3_SAP_LaunchApp` (or continue the login project). Keep everything inside the **Use Application/Browser** scope, **Wait for Ready = Interactive**.

    1. **Type Into** the launchpad **search** field → `"My Inbox"`. Capture it into the **Object Repository** as `LaunchpadSearch`.
    2. Add **Check App State** for the search results, then **Click** the **My Inbox** result to open the app. Capture it as `AppResultTile`.
    3. Add **Check App State** for an element that only appears once the app has loaded (the app title, or the task list) so you don't read too early.
    4. Add **Get Text** on the open-task count (or the first item's subject) → output `appInfo`. Target it by a stable attribute or an anchor.
    5. Add **Log Message** (Info): `$"My Inbox: {appInfo} open item(s)"`.
    6. Run and confirm the value is logged.

    **Expected result:** the automation searches the launchpad, opens the app, waits for it to load, reads the value, and logs it, with a **Check App State** guarding the search results and the app content so it never reads too early.

    !!! note "Choosing the value to extract"
        Read whatever the app reliably shows in your system, a count, a header, or the first row of a list. If the app can be empty, extract a value that is always present (the app title or a total that shows `0`) so the exercise still succeeds with no data.

[Next: Debugging, Errors & Logging](4-debugging-errors.md){ .md-button .md-button--primary }
