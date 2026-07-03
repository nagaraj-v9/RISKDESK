# RiskDesk
Futures Accounts System - Risk Engine and Account Management

A futures prop-firm risk calculator, position sizer, and trade journal — built for rotating multiple evaluation/funded accounts (LucidFlex, MFFU) without losing track of the rules that actually govern them.

Single HTML file. No build step, no framework, no backend required to run it.

**Live:** _add your GitHub Pages URL here once it's deployed_

---

## What it does

**Dashboard** — Live account status for whichever account is active: current equity, realized P&L, progress to profit target, drawdown cushion, daily-loss and drawdown meters, rule-compliance checklist, and a profitable-days tracker.

**Calculator** — Pick an instrument (NQ, MNQ, ES, MES, YM, MYM, RTY, M2K, GC, MGC, CL, MCL — real tick/point values), enter Entry/SL/TP, and it sizes the position automatically off your risk setting (fixed $ or % of balance), or lets you override with a manual contract count. Shows $ risk, $ reward, R:R, and flags if you're over max contracts or your daily loss buffer.

**Pre-Trade Journal** — Session, setup, liquidity, and a checklist (bias confirmed, session valid, liquidity identified, setup valid, risk within limits, daily rules allow another execution) gates the "Log Trade" button. Includes a live, real-time clock (updates every second) and Before-trade emotion tags (Fear / Confidence / FOMO).

**Daily Rule Engine** — Enforces a strict 2-trades-max daily system per account: Trade 1 loss → stop for the day. Trade 1 win → Trade 2 permitted. Trade 2, win or lose → stop. Shown as a live banner on the Calculator tab.

**Trade Log** — Full history per account, expandable per-row journal with Setup/Liquidity/Time/Checklist, plus During (Anxiety / Moved Stop / Impatience) and After (Revenge / Satisfaction / Frustration) tags you fill in once a trade closes. Filterable by account.

**Firm Rules** — Your daily trade system as a flowchart, an EOD trailing-drawdown breakdown (computed per account, including the "floor freezes at balance + $100" mechanic), the full LucidFlex hidden-rules list, and a LucidFlex vs MFFU comparison table.

**Acc Settings** — Add as many accounts as you rotate through (quick-add presets for LucidFlex/MFFU at 25K/50K/100K), each with its own firm, size, Evaluation/Funded phase toggle, risk mode, editable rule table, and an optional account ID/alias for your real broker login.

## Data & sync

By default, everything is stored in `localStorage` — it stays on the browser and device you're using, nothing leaves your machine.

Optional cloud sync is built in via [Supabase](https://supabase.com) (free tier): sign up, sign in from any device, and your accounts/trades follow you. It's off by default. To enable it:

1. Create a free Supabase project.
2. Run this in the Supabase SQL Editor:
   ```sql
   create table riskdesk_data (
     user_id uuid references auth.users(id) primary key,
     settings jsonb,
     trades jsonb,
     updated_at timestamptz default now()
   );
   alter table riskdesk_data enable row level security;
   create policy "Users manage own data" on riskdesk_data
     for all using (auth.uid() = user_id) with check (auth.uid() = user_id);
   ```
3. In **Authentication → Providers → Email**, turn off "Confirm email" (optional — smoother onboarding for friends).
4. In **Settings → API**, copy the Project URL and the `anon` public key.
5. In `index.html`, near the top of the `<script>` block, replace:
   ```js
   const SUPABASE_URL = 'YOUR_SUPABASE_URL';
   const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
   ```
   with your actual values.

Without this setup, the app runs entirely local-only — fully functional, just single-device.

## Running it

No install needed. Either:
- Open the HTML file directly in a browser, or
- Host it as a static site (GitHub Pages, Netlify, Vercel, etc.)

If deploying to GitHub Pages, name the file `index.html` at the repo root, then enable Pages in **Settings → Pages → Deploy from a branch → main → / (root)**.

## Tech

Plain HTML5, CSS3 (custom properties, Grid/Flexbox, no framework), vanilla JavaScript (ES6+, no build tooling, no npm dependencies). Google Fonts (Space Grotesk, Inter, IBM Plex Mono) loaded via CDN. Optional Supabase JS client via CDN for auth/cloud sync.

## Disclaimer

Firm rules (profit targets, drawdown limits, consistency rules, payout structure, etc.) are editable defaults based on publicly available terms at the time of writing and are **not guaranteed current**. Always verify against your actual funded account agreement before trading on these numbers. This tool is a risk-management aid, not financial advice.

## License

MIT — see [LICENSE](./LICENSE).

## Author

Nagaraj VK
mr.nagarajvk@gmail.com
