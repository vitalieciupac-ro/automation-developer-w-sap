# Setup

Have the installers and access below ready before the session. You don't need everything perfect beforehand: in the **first lesson** we install Studio, connect Orchestrator, set up licensing, and check the environment together.

## What you need installed

| Requirement | Notes |
|-------------|-------|
| **UiPath Studio, Version 25.10.17** | Community or Enterprise, licensed and opened at least once. |
| **Microsoft Excel** | Required for the modern Excel activities in Module 2. |
| **A test email account** | For Module 5. If live email isn't available, start from the provided PDF sample. |
| **UiPath Orchestrator (offered by UiPath)** | An Automation Cloud Community tenant is fine, and is used only for the Module 6 publishing demo. |
| **Access to an SAP S/4HANA environment** | For Module 3. Use your organization's system or an SAP trial. If this is not possible, Module 3 falls back to a learning web application where the same concepts apply. |
| **SAP GUI for Windows installed (SAP Logon)** | For Day 3. Install the desktop SAP client and connect it to your SAP system. Client-side GUI Scripting is enabled during Day 3. |

!!! warning "SAP access is a separate license"
    UiPath cannot provide an SAP environment of any kind. SAP is a licensed product, and access to any SAP system is granted by **SAP** under a separate commercial agreement, not by UiPath. UiPath automates SAP but does not resell or grant access to it, so this training cannot include an SAP environment. Use your organization's SAP system, or an SAP-provided trial. If none is available, Module 3 falls back to a learning web application that exercises the same UI automation concepts: selectors, synchronization, and the Object Repository.

## Sample files

[Download the sample files (ZIP)](https://uipath.sharepoint.com/:f:/s/GlobalPartnerEnablementNetwork/IgBZF_v2xyn7RbfoLzT8zMv-AYnxUBLJkKmM-7rhG3oq1Vc?e=0IbVnT?download=1){ .md-button .md-button--primary target="_blank" rel="noopener" }

Unzip them to a known local path, for example `C:\ADAF\Samples`:

```text
C:\ADAF\Samples\
├── sales_report.xlsx         # Region / Product / Amount (Module 2)
├── invoices\
│   └── sample_invoice.pdf    # native-text invoice (Module 5)
├── FragileProcess\          # ready-to-open project to harden (Module 4)
└── README.txt                # file overview and notes
```

[Start Module 1](../foundations/1-studio-orchestrator-licensing.md){ .md-button .md-button--primary }
