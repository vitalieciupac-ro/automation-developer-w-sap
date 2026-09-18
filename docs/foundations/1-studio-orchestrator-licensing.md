# 1. Studio, Orchestrator & Licensing

Before you build anything, get your tools in place. This module installs **UiPath Studio**, tours **Orchestrator** so you know what it manages, and configures the **license** you need to run an automation attended, on your own machine, started by you.

!!! abstract "What you'll do"
    - Install UiPath Studio and sign in.
    - Take a guided tour of Orchestrator and what it manages.
    - Configure a license for an attended run.

## Install UiPath Studio

Studio is where you build automations. It comes in two editions: **Community** (free, for individuals and small teams) and **Enterprise** (licensed). This course targets **Studio 25.10.17**.

!!! example "Build it together"
    1. Sign in to the **UiPath Automation Cloud** that the UiPath team gave you access to.
    2. From the Automation Cloud home or the **Resource Center**, download the Studio installer.
    3. Run the installer and, when prompted, sign in with your UiPath account.
    4. Choose the **Studio** profile for developer work.
    5. On first launch, confirm the version under **Help → About** (25.10.x).

Official documentation: [UiPath Studio](https://docs.uipath.com/studio).

## Orchestrator walkthrough

Orchestrator is the web platform that manages your robots and automations. You don't build here, but you **publish** to it, and it **runs and monitors** what you publish. Tour the key areas:

- **Tenants and Folders**: how work is organized and access is scoped.
- **Machines and Robots**: the runtimes that execute automations.
- **Processes and Jobs**: a *Process* is a published automation; a *Job* is a single run of it.
- **Assets and Queues**: shared configuration values, and work items processed at scale.
- **Triggers**: run automations on a schedule or an event.
- **Monitoring and Logs**: see what ran, when, and what happened.

Connecting Studio to Orchestrator (sign in from Studio) lets you publish your project to a feed and run it.

Official documentation: [UiPath Orchestrator](https://docs.uipath.com/orchestrator).

## License configuration for an attended run

An **attended** robot runs on a person's own machine and is started by that person, under their supervision. To run one, the user needs an attended-capable license. Any of these allow attended runs: **Attended**, **Citizen Developer**, **RPA Developer**, or **Automation Developer**.

!!! example "Build it together"
    1. In **Automation Cloud**, an Organization Administrator activates the license and confirms the tenant has an available attended (or Automation Developer) license.
    2. **Assign** the license to your user, and make sure **Enable user to run automations** is turned on.
    3. In the **UiPath Assistant**, use **Interactive Sign-In** to connect your machine to Orchestrator (the **Client ID** connection type, with the client credentials).
    4. Confirm in the Assistant that the robot shows as **connected and licensed**.

    You can now run published automations attended from the Assistant.

Official documentation: [Licensing robots for attended automations](https://docs.uipath.com/robot/standalone/latest/admin-guide/licensing-robots-attended).

!!! note "Attended vs unattended"
    Attended automations run under human supervision, triggered by the user from the Assistant, and suit smaller interactive tasks. Unattended robots run on their own machines, triggered from Orchestrator, with no human present. This course sets up **attended**.

[Next: DataTables & Excel](2-datatables-excel.md){ .md-button .md-button--primary }
