# HR Portal — Attendance & Payslips

Static web app (GitHub Pages). HR signs in with a password, marks daily attendance, and the admin downloads payslip PDFs. Data is stored **encrypted** in a JSON file in this GitHub repo.

## How it works
- `index.html` – the app (Attendance · Employees · Payslips · Settings)
- `setup.html` – one-time page that creates `config.js` (your GitHub token, encrypted with your password)
- `config.js` – connection settings. Safe to commit: the token inside is AES-encrypted with your password
- `data/hr-data.json` – created automatically on first save. Employee details, salary, PAN, bank and attendance are all encrypted (AES-GCM, PBKDF2 250k iterations). Without the password it is unreadable

## One-time setup
1. Create a GitHub repo (e.g. `hr-portal`) and upload these files (`index.html`, `setup.html`, `config.js`, `.nojekyll`).
2. Settings → Pages → Source: `main` branch, `/ (root)`.
3. Create a **fine-grained personal access token**: GitHub → Settings → Developer settings → Fine-grained tokens.
   - Repository access: *Only select repositories* → this repo
   - Permissions: **Contents → Read and write**
   - Set an expiry you are comfortable with (renew it by re-running setup)
4. Open `https://<user>.github.io/<repo>/setup.html`, enter username, repo, token and a strong password (10+ characters). Press **Create config.js**, download it, and replace `config.js` in the repo. Commit.
5. Open `https://<user>.github.io/<repo>/` and sign in.

## Daily use (HR)
- **Attendance tab**: click a day cell to cycle `P → A → L → HD → H → WO → blank`. Click a **day number** to mark everyone Present for that day. **Mark unmarked days up to today as Present** fills the rest.
- Changes auto-save to GitHub a moment after each edit (status shown top-right).

## Rules built in
- **LOP:** Absent = 1 LOP day, Half day = ½ LOP, Leave beyond the available balance = LOP.
- **Pro-rata salary:** earnings × Paid Days ÷ Days in Month (can be switched off in Settings). Deductions are fixed amounts.
- **Leave accrual:** credit (default 2 days) at each month end, usable from the next month. A new joiner gets no credit for the joining month; the first credit is at the end of the following month.
- Days before date of joining are blocked. Week-off days (default Sunday) are configurable.

## Employees
Add employees in the **Employees** tab: details, opening leave balance (and the month it is as of), and their monthly earnings and deductions. Payslips use the employee's current salary structure.

## Payslips
Choose the salary month, tick employees, **Download payslips (PDF)** – one A4 page per employee, or **PDF** on a single row. **Export summary (CSV)** gives a payroll sheet.

## Security notes
- This is client-side protection: anyone who can load the site can attempt to sign in, so use a long password. Keep the token limited to this one repo.
- A **public repo exposes** `config.js` (encrypted token) and the encrypted data file. A **private repo** is safer but GitHub Pages for private repos needs a paid plan; alternatively host the same files elsewhere.
- If two people save at the same time, the later save is rejected with a message — use Settings → *Reload data*, then redo the change.
- Change the password from Settings; then immediately replace `config.js` in the repo with the file it downloads.
- Never commit the plain token. `setup.html` never stores or sends it anywhere except to `api.github.com`.
