# Quote Approval Frontend (GitHub Pages)

## Project Overview
External quote approval page for AVRAHAM ADVERTISING LTD. Deployed on GitHub Pages, integrates with BASE44 backend.
Single-file app: `index.html` (HTML + CSS + JS, RTL Hebrew)

## Architecture
- **Frontend**: GitHub Pages at `https://avraham-prog.github.io/quote-approval/`
- **Backend**: BASE44 serverless functions (Deno Deploy) at `https://avraham-ap-flow.base44.app/functions/`
- **Data API**: `https://app.base44.com/api/apps/699c51009912f4002ab9900b/entities/{EntityName}` (public, no auth)
- **App ID**: `699c51009912f4002ab9900b`

## API Endpoints
- `GET /functions/getQuote?token={signature_token}` — returns quote data with items
- `POST /functions/approveQuote` — body: `{ token, signer_name, signature }` — approves quote

## BASE44 Entity Field Mappings
### Quote
`quote_number`, `quote_date`, `quote_type`, `customer_name`, `contact_name`, `contact_phone`, `employee_name`, `title`, `payment_terms_text`, `intro_text`, `status`, `signature_token`, `approval_token`, `signer_name`, `signature_url`, `signed_at`, `approved_date`
- `quote_type` values: `"detailed_gross_net"` (shows bruto+neto), `"detailed_net"` (neto only)

### QuoteLine
`quote_id`, `description`, `vendor_name`, `activity_type`, `quantity`, `line_order`, `client_gross`, `client_net`, `vendor_gross`, `vendor_net`, `vendor_commission_pct`, `client_commission_pct`, `profit_margin_pct`, `profit`, `department`, `air_date_start`, `air_date_end`

## Key Business Logic
- **Description fallback**: `l.description || l.vendor_name || l.activity_type || ''` — activity rows (e.g., Google) have empty `description`, use `vendor_name`
- **Quantity display**: Only show for price-list rows (`activity_type.startsWith("מחירונים")`), not for activity rows (e.g., `"פרפורמנס"`)
- **Bruto column**: Controlled by `quote_type` field. `"detailed_gross_net"` shows bruto crossed-out + neto; `"detailed_net"` hides bruto column entirely
- **Signature flow**: After approval, hide signing form, show `approvedSignature` div with signer name + signature image + date

## BASE44 Deployment (CRITICAL)
- **Monaco editor `setValue()` does NOT trigger "Save & Redeploy"** — BASE44 doesn't detect programmatic changes
- **Use BASE44 AI chat** to edit functions — it triggers proper save/deploy cycle
- **AI chat input**: Use `form_input` tool (not `computer.type()` which garbles Hebrew)
- **"Test Function" does NOT deploy** — only tests locally
- **"Publish" button** deploys both frontend and functions
- **Functions URL format**: `https://avraham-ap-flow.base44.app/functions/{functionName}`
- **API info panel**: Click "API" button at bottom-right of function editor to see endpoint URL

## Git & Deploy
- `git push` to `main` → auto-deploys to GitHub Pages
- GitHub Pages may cache; allow ~30s after push for updates
- Repo: `https://github.com/Avraham-prog/quote-approval`
