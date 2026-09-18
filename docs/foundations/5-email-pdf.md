# 5. Automating Applications, Email & PDF

Email and PDF are where automation earns its keep: invoices, reports, and confirmations. The pattern is almost always the same: **fetch a message, save its attachment, read the document, extract fields, record them.**

!!! abstract "What you'll be able to do"
    - Send and read email, and save attachments, with the modern mail activities.
    - Extract text from native and scanned PDFs.
    - Combine email + PDF + Excel into a short end-to-end flow.

## Email essentials

Work inside a mail scope, **Use Desktop Outlook App**, or the Gmail / SMTP-IMAP options.

- **Get Mail Messages** with a filter and *Top N* to limit what you pull.
- Read properties: `Subject`, `From`, `Body`.
- **Save Attachments** to a folder; **Send Mail** to reply or forward.
- Use the **Unread** filter and **Mark as Read** so you don't reprocess the same message.

## PDF essentials

Two activities to know:

- **Read PDF Text**: fast and exact, for native/digital PDFs.
- **Read PDF With OCR**: for scanned/image PDFs; needs an OCR engine, slower and less precise.

!!! info "Which PDF activity?"
    Can you select the text with your mouse? Use **Read PDF Text**. Is it a picture of a document? Use **Read PDF With OCR**. The result is one big string. Pulling out a specific field is a string or Regex problem.

!!! autopilot "Draft the flow with Autopilot"
    **How:** in the **Autopilot** panel, describe the intake in plain language, for example *"get the latest unread invoice email, save the PDF attachment, read its text, and log the invoice number and total"*. Autopilot inserts a first-draft sequence; wire up the real folder paths and the extraction logic yourself, and confirm each activity is the one you want.

---

## Try it: Invoice Intake

!!! example "Scenario"
    Invoices arrive by email as PDF attachments. Build the intake step: fetch the invoice email, save its PDF, extract the invoice number and total, and log them for the finance queue.

**Packages:** `UiPath.Mail.Activities` and `UiPath.PDF.Activities` (plus the default `UiPath.System.Activities`, which provides the Regex Matches activity).

**What to produce**

- A process that gets the latest unread *Invoice* email, saves the PDF attachment, reads its text, extracts the invoice number and total, and logs `INV-##### | total`.

**You are given**

- A test mailbox with an invoice email, **or** the offline sample: `invoices\sample_invoice.pdf` plus a saved `.eml`.
- Invoice number pattern: `INV-\d{5}`. Total appears after the label `Total:`.

**Hints**

- Use a mail scope + **Get Mail Messages** with an unread + subject filter, *Top 1*.
- **Save Attachments** to a folder, then **Read PDF Text** on the saved file.
- The PDF text is one big string, use **Matches** (Regex) for the number, string methods for the total.
- If the PDF is scanned (can't select its text), switch to **Read PDF With OCR**.

??? success "Solution"
    Create `Ex5_InvoiceIntake`. If email isn't available, start from step 4 using the sample PDF.

    1. Add the mail scope and **Get Mail Messages**: filter *Unread*, Subject contains `"Invoice"`, *Top 1* → output `messages`.
    2. For the first message, add **Save Attachments** to a folder → you now have the PDF path.
    3. **Mark as read** (activity option) so it isn't reprocessed.
    4. Add **Read PDF Text** on the saved PDF → output `pdfText`.
    5. Add **Matches** (Regex) on `pdfText` with pattern `INV-\d{5}` → take the first match as `invoiceNo`.
    6. Extract the total: **Assign** `totalLine = pdfText.Split(Environment.NewLine.ToCharArray()).First(Function(l) l.Contains("Total:"))`, then `total = totalLine.Split(":"c)(1).Trim()`.
    7. Add **Log Message** (Info): `$"{invoiceNo} | {total}"`.
    8. Run. Confirm the log shows the correct invoice number and total.

    **Expected result:** one log line such as `INV-04217 | 12,480.00`, with the PDF saved to the target folder and the email marked read.

!!! question "Debrief"
    This flow just used Modules 1, 2, and 5 together. Which piece came from where? Real automations *compose* skills, they rarely use just one.

[Next: Professional Projects](6-professional-projects.md){ .md-button .md-button--primary }
