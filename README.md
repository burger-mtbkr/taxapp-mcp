<img src="icon-256.png" alt="Tax App logo" width="96" height="96">

# taxapp.nz MCP server

Ask your AI app about your [Tax App](https://taxapp.nz) records in plain words: income, expenses, rental properties
and New Zealand tax. Where your organisation allows it, it can add and change records too.

Tax App is a New Zealand app for households, sole traders and rental-property owners. It keeps income, expenses,
contacts, rental properties and bank reconciliation in one place and works out indicative tax from Inland Revenue's
rates. This MCP server is a remote server hosted by Tax App; this repository describes it and holds no code.

- Server address: `https://mcp.taxapp.nz/mcp` (remote, Streamable HTTP)
- MCP Registry name: `nz.taxapp/taxapp`
- Full guide: https://taxapp.nz/guides/ai-assistants/

## Sign-in required

Every request needs you to sign in to Tax App. Your AI app opens the Tax App sign-in page when you connect; there is
no key or password to copy into it.

## Before you connect

1. You need a Tax App account. [Sign up free](https://app.taxapp.nz/signup).
2. An owner of your organisation turns AI connectors on in **Settings > AI connectors** (or while connecting) and
   chooses **Read only** or **Read, add and change records**.
3. Add the server in your AI app and sign in to Tax App when asked.

## Configure it

### VS Code (GitHub Copilot)

Add it from the `@mcp` gallery, or put this in your `mcp.json`:

```json
{
  "servers": {
    "taxapp": {
      "type": "http",
      "url": "https://mcp.taxapp.nz/mcp"
    }
  }
}
```

Start the server, choose **Allow** when VS Code asks to sign in, sign in to Tax App, and tick the organisations it may
reach.

### Other AI apps

| App | How |
|-----|-----|
| Claude (web, desktop, mobile) | Settings > Connectors > Add custom connector, name `taxapp`, URL `https://mcp.taxapp.nz/mcp` |
| Claude Code | `claude mcp add --transport http taxapp https://mcp.taxapp.nz/mcp` |
| ChatGPT | Settings > Apps (developer mode on) > create, URL `https://mcp.taxapp.nz/mcp`, authentication OAuth |
| Cursor | Add `"taxapp": { "url": "https://mcp.taxapp.nz/mcp" }` under `mcpServers` in `~/.cursor/mcp.json` |

## How to use it

Ask in plain words, for example:

- "What is my estimated tax for this year?"
- "Which expenses did I record for the rental in March?"
- "Which bank transactions still need sorting?"
- "Add a $120 plumber bill for 12 Smith Street."
- "Add Jane Brown as a tenant at 12 Smith Street from 1 May at $650 a week."
- "The rent at 12 Smith Street goes up to $680 a week from 1 June."
- "Change last week's commission payment to $1,250."

The figures come straight from your records. The answer is written by the AI, so check anything that matters in the
app. When a detail is unclear, such as which contact, property or expense account you mean, the AI app shows you the
closest matches and asks.

Tax years are New Zealand income years (1 April to 31 March), named by the year they end in. Money is in New Zealand
dollars.

## Tools

| Area | Read | Add and change |
|------|------|----------------|
| Organisations | `list_organisations` | |
| Choices | `list_choices` (income, expense and contact types, IRD return lines, in your own words) | |
| Tax year | `get_tax_year_overview`, `get_tax_summary`, `get_profit_and_loss` | |
| Income | `get_income_history`, `list_income`, `get_income_record` | `create_income` (commission, rent or tenant reimbursement), `update_income` |
| Expenses | `list_expenses`, `get_expense_record`, `list_expense_accounts` | `create_expense`, `update_expense`, `create_expense_account`, `update_expense_account` |
| Contacts | `list_contacts`, `list_contact_types` | `create_contact`, `update_contact`, `set_contact_paye_salary`, `set_contact_acc_classifications`, `create_contact_type`, `update_contact_type` |
| Rental properties | `list_properties` (with tenancies), `get_rental_report` | `create_property`, `update_property`, `create_tenancy`, `update_tenancy` |
| Savings | `get_savings_plan` | `update_savings_plan`, `copy_savings_plan_from_previous_year` |
| Scheduled reports | `list_scheduled_report_types`, `list_scheduled_reports` (owners) | `create_scheduled_report`, `update_scheduled_report` (owners), `send_scheduled_report_now` |
| Bank | `get_bank_inbox`, `get_reconciliation_status` | |

Every tool is annotated: the read tools are read-only, the tools that change what is already there are marked
destructive, and sending a report is marked as reaching outside Tax App. Adding and changing are refused unless an
owner chose **Read, add and change records**, and never go beyond your own role. The AI app sends only what you tell
it, such as dates, amounts and who paid: Tax App works out GST, KiwiSaver, ACC levy, tax and every total itself,
exactly as it does on its own forms. Adding is safe to repeat: the same details sent again return the record already
made. A change sends only the fields that change.

## What it cannot do

- Delete anything
- Reconcile bank statements or change a match
- Change organisation settings, members or connections to other services
- See anything your own role cannot, or an organisation you did not tick
- File a return with Inland Revenue

## Keeping it safe

- It signs in as you and never sees your password.
- Every change it makes is in the organisation's audit trail, marked as made through the AI connector.
- Owners see every AI connector under **Settings > AI connectors** and can remove any of them; switching AI connectors
  off ends them all.
- Changing your password ends every AI connector you made.
- What you share goes to the company behind your AI app, under its own terms, and may be kept outside New Zealand.
  Your records in Tax App stay where they are, in New Zealand.
- Tax, ACC and KiwiSaver figures are indicative workings from IRD rates, not an IRD assessment.

## For app developers

- Authentication: OAuth 2.1 with PKCE (S256). A request without a valid sign-in gets HTTP 401 with a
  `WWW-Authenticate` challenge.
- Authorization server: `https://api.taxapp.nz` (dynamic client registration and client ID metadata documents)
- Protected resource metadata: `https://mcp.taxapp.nz/.well-known/oauth-protected-resource/mcp`
- Scope: `taxapp`. No API key, header or environment variable to configure.

## More

- [Full guide](https://taxapp.nz/guides/ai-assistants/)
- [Privacy policy](https://taxapp.nz/privacy/)
- Help: support@taxapp.nz
