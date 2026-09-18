# Best practices: classic RPA & SAP

A short session to consolidate the day and the course. First the general UiPath practices you have used throughout, then SAP-specific practices that come from real project experience.

## Classic UiPath implementation

- **Keep it modular.** One workflow, one job. Compose with **Invoke Workflow File** and pass data through **arguments**, so `Main` reads like a table of contents.
- **Externalise configuration.** Keep URLs, paths, and credentials in a config file or in **Orchestrator Assets**, not hardcoded in activities.
- **Handle errors deliberately.** **Try Catch** with specific exception types, **Retry Scope** for transient failures, and a **Global Exception Handler** as the safety net.
- **Log at the right level**, and use **Add Log Fields** to attach context (an ID, a record key) to every later message.
- **Target reliably.** Prefer stable selectors and the **Object Repository**, and synchronise with **Check App State** instead of fixed Delays.
- **Validate and version.** Run **Workflow Analyzer** before review, keep the project in **source control**, and publish to a feed.
- **Scale from here.** For larger processes, the **Robotic Enterprise Framework (REFramework)** and **Orchestrator Queues** are the next step beyond this course.

## SAP-specific best practices

### Environment and account setup

- **Develop against a sandbox.** Use a test SAP environment populated with data, never production, to avoid hazardous changes.
- **Plan for password expiry.** SAP passwords expire without notice; the user is simply prompted for a new one. In your login, detect the **New Password** screen and alert the right people by email or an Orchestrator alert. Having the robot change the password automatically is possible but less secure.
- **Enable GUI Scripting** on the client and the server. As covered on Day 3, this is not a security risk: a script has the same rights and data-validation as the user who started it. Without scripting you fall back to image and OCR, which is harder for no real gain.
- **Mind display formats.** Date, number, and currency formats are **per-user** settings (transaction `SU3`), so each account can differ. Either standardise them across the project, or have the robot set or verify them at login and raise an error if they are not as expected.
- **Watch session limits.** Consider the number of sessions a user is allowed when several robots share one SAP account, or a robot works while the business user is logged in.

### Beyond the UI: Integration Service for SAP

Not every SAP integration needs the screen. UiPath **Integration Service** provides pre-built, managed **connectors** that call SAP through its APIs instead of automating SAP GUI or Fiori, which is usually faster and more robust where an API exists. The main SAP connectors are:

- **SAP OData**, which integrates with **SAP S/4HANA** (and SAP Cloud for Customer) through OData APIs, and suits end-to-end processes such as procure-to-pay and order-to-cash.
- **SAP BAPI**, which executes SAP **BAPIs and RFCs** against SAP business objects, through a single dynamic *Execute BAPI/RFC* activity.

There are also SaaS connectors across the SAP portfolio, such as SAP Concur and SAP Cloud for Customer. You create a connection in Integration Service, then use the connector's activities in Studio. Integration Service runs in the cloud, so the SAP endpoints must be reachable over the public internet; private or on-premises systems may need firewall allowlisting, and the BAPI connector does not support on-premises servers.

Rule of thumb: use an Integration Service connector when a suitable SAP API exists, and fall back to UI automation (Fiori or SAP GUI) only when it does not.

Official documentation: [SAP OData connector](https://docs.uipath.com/integration-service/automation-cloud/latest/user-guide/uipath-sap-odata) and [SAP BAPI connector](https://docs.uipath.com/integration-service/automation-cloud/latest/user-guide/uipath-sap-bapi).

### Interacting with the SAP UI

- Most SAP GUI elements support **API interaction**, so **Simulate Click** and **Simulate Type** work well with them.
- **Simulate Type** into a field that cannot hold that many characters throws an error. **Truncate** long text to the field length.
- **Double Click does not work while Simulate Click is enabled.** First look for another route: a normal click plus an action button (process, execute) often has the same effect. If there is no alternative, use **Double Click** with the default **Hardware Events** setting.
- Prefer **buttons over hotkeys** to navigate. If a hotkey is unavoidable, give the activity a **specific selector** so it is not sent to the whole screen.
- **Extracting a table.** SAP usually does not load the whole table at once, so scrape in a **loop**, sending **Page Down** until the newly scraped rows match the previous batch (no new rows).
- **Filling a table faster than row by row.** Count the visible rows, build a string with cells separated by **tabs** and rows by **newlines**, paste it into the visible rows all at once, then send **Page Down** for the next empty batch.

### Tips and tricks

- Use the **SAP page title** to confirm the robot landed on the screen you expect.
- Read the **status bar** (bottom-left) for warnings when the expected flow cannot continue.
- Many SAP warnings clear by pressing **Enter** (the green check button).
- Consider **starting and stopping SAP at each transaction**. The login is quick, and it helps avoid memory leaks and caching issues that can crash SAP.

### Error handling

When an exception occurs:

1. **Log** the exception source and message.
2. **Take a screenshot.** SAP behaves differently depending on the input it receives, and a screenshot tells you a great deal about what went wrong.
3. Optionally capture the **page title** and **status-bar** message (not essential if you have the screenshot).
4. **Restart SAP** before the next item.

[See your next steps](../next-steps.md){ .md-button .md-button--primary }
