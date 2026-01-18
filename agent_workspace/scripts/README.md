# Scripts Memory & Knowledge Base

This directory contains persistent browser automation scripts and knowledge for Openwork.

## Scripts

### 1. `gmail_ref.mts`
**Description:** A reference implementation for Gmail automation provided by the user.
**Key Features:**
- Selectors for "To", "Subject", "Body".
- Attachment handling logic.
- "Message sent" verification.

### 2. `send_email_attachment.mts`
**Description:** A working, tested script that sends an email with an attachment.
**Current Configuration:**
- **Recipient:** tcasavan@woodproductsinc.net
- **Subject:** testtest
- **Attachment:** /Users/tec/Documents/Screenshot_Data.csv
*Note: Edit the constants at the top of the file to reuse for other emails.*

## Usage

To run these scripts, use the Openwork dev-browser environment:

```bash
cd /Applications/Openwork.app/Contents/Resources/skills/dev-browser
PATH="${NODE_BIN_PATH}:$PATH" npx tsx /Users/tec/Code/scripts_memory/send_email_attachment.mts
```
