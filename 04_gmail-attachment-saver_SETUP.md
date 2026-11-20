# Gmail Attachment Saver — Setup Guide

**What it does:** every hour, this workflow checks your Gmail, saves every attachment to Google Drive as `date_sender_original-name`, and writes one row per file to a Google Sheet — so emailed invoices, receipts and signed forms stop getting buried in your inbox.

**Time needed:** about 15 minutes. **You need:** an n8n account (cloud or self-hosted), a Gmail account, Google Drive, and Google Sheets.

> Honest note: this workflow is structure-checked — the JSON parses, every connection points at a real node, and the Code nodes were reviewed line by line. It has not been live-import-tested in a running n8n instance, so expect to pick your own accounts, folder and sheet as you go through the steps below. That is normal for imported templates.

## Steps

1. **Import.** In n8n, go to Workflows → ⋯ → *Import from File* and choose `04_gmail-attachment-saver.json`.
2. **Connect Gmail (node 1).** Open *1. New Email Arrives*, create/select your Gmail credential, and leave **Simple: OFF**. This matters — with Simple on, n8n strips attachments out of the trigger output and the workflow will never save anything.
3. **Make the log sheet.** In Google Sheets, create a spreadsheet called **Attachment Log** with one tab. Put these headers in row 1, in this order:
   `saved_at | file_name | drive_file_name | sender | subject | message_id | drive_link | drive_id`
4. **Point both Sheet nodes at it.** In *3. Check Saved Log* and *9. Log to Sheet*, connect your Google account and select the Attachment Log sheet. Node 3 reads it (the duplicate check); node 9 appends one row per saved file.
5. **Pick the Drive folder (node 7).** Open *7. Save to Google Drive*, connect your Drive account, and choose a destination folder — creating one called `Email Attachments` first keeps things tidy. If you skip this, files land in your Drive root.
6. **Test it.** Click *Execute Workflow*, then send yourself an email with a PDF attached. Within a minute you should see the file appear in Drive with a name like `2026-11-20_your-name_invoice.pdf`, and a matching row in the log sheet.
7. **Turn it on.** Save the workflow and flip the **Active** toggle (top right). It now checks your inbox every hour on its own.

## Good to know

- **No duplicates.** Before saving, the workflow checks the log: if the same message ID *and* file name are already logged, the attachment is skipped. Re-running or re-importing won't fill Drive with copies.
- **Your mail is untouched.** The workflow only reads Gmail. Nothing is deleted, moved, archived or labelled.
- **Emails without attachments are ignored** — no file, no log row, no noise.
- **File names** are `YYYY-MM-DD_sender_original-name`. Files sort by date, group by sender, and two files with the same name can never overwrite each other.

## If something doesn't run

- *Nothing happens at all* → check node 1: is Simple really OFF, and is the workflow Active?
- *Files land in Drive root* → node 7 has no folder picked yet; choose one and save.
- *Log rows are empty* → node 9's sheet must have the exact headers from step 3 in row 1.

Questions: support@workflowstash.com

---

**Want it wired up for you?** Custom n8n Workflow Build — $99 flat: the workflow built around your accounts, tested end to end, two revision rounds included. https://ko-fi.com/workflowstash (see Services)
