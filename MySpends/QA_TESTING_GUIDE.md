# MySpends — QA Testing Specification & Coverage Matrix

**Target Application:** MySpends Android Application  
**Package Name:** `com.orbit.myspends` / `com.myspends`  
**Document Purpose:** Complete QA testing plan, test suites, edge case verification, and release sign-off matrix for QA engineers.

---

## 1. Executive Summary & Architecture Overview

MySpends is a **100% offline-first personal finance tracking application** for Android. It reads financial bank SMS messages directly on the device, extracts transaction records (Debits, Credits, Transfers, UPI, Cards, ATM, NetBanking), categorizes them, tracks budgets, and provides multi-month visual analytics.

### Core Architecture Pillars to Test:
1. **Local-First Privacy:** No financial data or SMS body should ever leave the device over the network.
2. **Dual Conversion Engine:** Uses daily historical exchange rates via Frankfurter API with full offline fallback tables (2021–2026+).
3. **Resilient SMS Parser Matrix:** 40+ bank parsers with smart filters (excluding OTPs, spam, promotional ads, and credit card limit hikes).
4. **Non-Destructive Scanning:** Safe merge algorithms ensuring existing edited transactions, receipts, or notes are never destroyed on rescan.

---

## 2. Comprehensive Test Suites

### Suite 1: Onboarding, Permissions & First-Time Experience (FTUE)

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **ONB-01** | First launch permission prompt | App requests `READ_SMS`, `RECEIVE_SMS`, and `POST_NOTIFICATIONS` with clear explanatory rationale dialogs. | P0 |
| **ONB-02** | Permission denial handling | If user denies SMS permission, show graceful fallback explaining manual transaction entry; app must not crash. | P1 |
| **ONB-03** | Onboarding Engagement Screen | Introduces automatic SMS tracking; displays visual scan engagement card before starting initial scan. | P1 |
| **ONB-04** | Initial Historical Scan Depth | Historical scan initiates across inbox up to 2 years back without hitting timeouts, showing a live percentage/progress bar. | P0 |
| **ONB-05** | Onboarding Tip Card | Tip card is displayed on the dashboard for first-time users and can be permanently dismissed. | P2 |

---

### Suite 2: SMS Ingestion, Parsing Engine & Filters

The heart of MySpends is accurate bank SMS parsing. QA must test both **incoming real-time SMS** (`BroadcastReceiver`) and **historical batch scanning** (`SmsReader`).

#### A. Supported Banks & Formats
Verify parsing accuracy across major Indian and international banks:
- **Public & Private Banks:** HDFC, ICICI, SBI, Axis, Kotak, PNB, Bank of Baroda, Canara, Union Bank, IndusInd, Yes Bank, IDFC First, Federal Bank, RBL, Bandhan, IDBI, Central Bank, UCO Bank, City Union Bank (CUB), Andhra Pragathi Grameena Bank (APGB), HSBC, Standard Chartered, Amex.
- **Payment Providers & Wallets:** Paytm, PhonePe, Google Pay, Amazon Pay, Airtel Payments Bank.

#### B. Key Parser Test Cases

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **SMS-01** | Standard Debit Transaction | SMS containing `debited by INR 1,500.00 on xx/xx at Merchant via UPI` parsed into a Debit transaction with correct Amount, Bank, Date, and Merchant. | P0 |
| **SMS-02** | Standard Credit / Salary | SMS containing `credited with Rs 75,000.00` parsed as Income/Credit with correct account number suffix. | P0 |
| **SMS-03** | Credit Card Swipe / POS | Extracted as Debit, card last 4 digits captured, marked as `Credit Card` payment mode. | P0 |
| **SMS-04** | ATM Cash Withdrawal | Parsed as `ATM Withdrawal`, category set to `Cash / ATM`. | P1 |
| **SMS-05** | Credit Card Bill Payment Exclusion | When paying a credit card bill from savings account, debit is logged, but receiving CC SMS confirmation must **not** be double-counted as income. | P0 |
| **SMS-06** | Credit Card Limit Hike Exclusion | SMS containing `Your credit limit has been increased to Rs 2,00,000` must be **completely ignored** (not logged as transaction). | P0 |
| **SMS-07** | OTP & 2FA Exclusion | Messages containing `OTP is 123456`, `valid for 10 mins`, `do not share` must be skipped. | P0 |
| **SMS-08** | Promotional & Ad Filter | SMS with `Flat 50% off on Swiggy`, `Pre-approved personal loan`, `Get cashback` must be filtered out by `SmsPromotionalFilter`. | P0 |
| **SMS-09** | Reminder & Unpaid Bill Filter | SMS saying `Bill generated for Rs 999 due on 15th` must **not** create a spend until the actual debit occurs. | P1 |
| **SMS-10** | Duplicate Detection | Receiving the identical SMS twice (or historical rescan over existing data) must not create duplicate transactions. | P0 |
| **SMS-11** | UPI Handle Disambiguation | UPI handles like `@icici`, `@okhdfcbank`, `@axisbank` in a body must not misattribute the user's actual sending bank. | P1 |

---

### Suite 3: Multi-Currency & Historical FX Rate Conversion

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **FX-01** | Foreign Currency Detection | Transaction in `USD`, `EUR`, `GBP`, `AED`, `SGD` properly parsed with original foreign currency code. | P0 |
| **FX-02** | Transaction Date FX Rate (Online) | App fetches the historical rate on the **exact transaction date** via Frankfurter API (`api.frankfurter.dev/v1/{YYYY-MM-DD}`). | P0 |
| **FX-03** | Weekend / Holiday Rate Carryover | If transaction occurred on a Sunday or bank holiday, API/Fallback resolves to the preceding business day rate. | P1 |
| **FX-04** | Offline Fallback Rate Table | Turn off Wi-Fi/Mobile Data. Foreign transactions for 2021–2026 must successfully convert using built-in offline tables without failing or showing 0. | P0 |
| **FX-05** | Persistent Disk Cache | Verify conversion rates are cached locally in SharedPreferences (`hist_{date}_{currency}`). Repeated views must not trigger extra network requests. | P1 |
| **FX-06** | UI Rate Badge | Transaction details display an informative tag: `Rate on dd MMM yyyy (1 USD = xx.xx INR)`. | P2 |

---

### Suite 4: Bank Branding, Logos & Universal Fallback

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **ICO-01** | Major Bank Logo Display | Verify known banks (HDFC, SBI, ICICI, Axis, Kotak, Amex) render their authentic brand logos. | P1 |
| **ICO-02** | Extended 100+ Banks | Verify logos/badges for RBL, Bandhan, IDBI, Central Bank, Indian Bank, UCO, APGB, CUB, AU Small Finance. | P1 |
| **ICO-03** | Unknown Bank Fallback | If an unlisted cooperative or rural bank is parsed: | P0 |
| | a) App does **not** crash or show a broken image. | |
| | b) Displays the architectural bank facade vector (`ic_bank_generic`). | |
| | c) Background badge generates a deterministic, harmonious brand tint based on the bank name string. | |

---

### Suite 5: Dashboard & Budget Management

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **DSH-01** | Monthly Summary Metrics | Total Spends, Income, and Balance match the exact sum of transactions in the active month. | P0 |
| **DSH-02** | Inactive Bank Hiding | Banks with 0 spends and 0 credits in the selected period are hidden from the dashboard bank breakdown. | P1 |
| **DSH-03** | Interactive Bank Cards | Tapping on any bank row navigates to the Transactions screen pre-filtered for that specific bank. | P1 |
| **DSH-04** | Budget Progress & Warning | Spending reaching 80% (default threshold) reflects warning state and triggers budget notification alert. | P1 |
| **DSH-05** | Save Budget / Limit Isolation | Editing monthly budget or bank limit in Profile/Settings saves immediately **without** triggering a background SMS rescan. | P0 |

---

### Suite 6: Transactions List, Filtering & Actions

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **TXN-01** | Global Calendar Filter | Quick tabs (*This Month*, *Last Month*, *Last 90 Days*, *Custom Range*) instantly update the list with correct dates. | P0 |
| **TXN-02** | Multi-Bank Filter | Selecting multiple banks filters the list to include transactions from all selected banks. | P1 |
| **TXN-03** | Category & Type Filters | Filter by Debit / Credit / All, and by specific categories (Food, Shopping, Utilities, Travel). | P1 |
| **TXN-04** | Active Filter Chips | Active filters appear as removable chips above the list; tapping `X` clears that specific filter. | P2 |
| **TXN-05** | Manual Transaction Add/Edit | Adding or editing a transaction uses exposed dropdown menus; saves seamlessly to Room DB and updates dashboard. | P0 |
| **TXN-06** | Transaction Deletion | Deleting a transaction shows confirmation dialog, removes entry, and updates category totals. | P1 |
| **TXN-07** | Slide Navigation | Opening transaction detail uses custom smooth slide animation (`slide_in_right` / `slide_out_left`). | P2 |
| **TXN-08** | Receipt Card Sharing | Tapping "Share Receipt" generates a clean, branded receipt image and launches Android system share sheet. | P1 |

---

### Suite 7: Settings & Safe Historical Rescan

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **SET-01** | Date-Range Rescan Modal | User can choose Custom Date Range (e.g., last 3 months, 6 months, 1 year, or full history). | P0 |
| **SET-02** | Refresh Animation Polish | Scan dialog shows dedicated vector refresh icon (`ic_sync_refresh`) spinning at a gentle, smooth 2000ms speed. | P2 |
| **SET-03** | Non-Destructive Rescan | Rescanning must **not** delete manual edits (e.g., custom category assignments or notes) or create duplicates. | P0 |
| **SET-04** | Cancel Rescan | Tapping Cancel cleanly halts background scanning worker without corrupting database state. | P1 |

---

### Suite 8: Analytics, Insights & Export

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **ANA-01** | Chart View Toggle | Switching between Donut 🍩, Bar 📊, and List 📋 modes updates accurately with zero flicker. | P1 |
| **ANA-02** | Multi-Month Spend Trend | Compares 2-3 months accurately, highlighting anomalous category spikes. | P1 |
| **ANA-03** | Financial Health Score | Computes 0–100 score based on savings ratio and spending discipline; generates tailored tips. | P2 |
| **ANA-04** | CSV Export Consent Dialog | Exporting data prompts an explicit Export Consent Dialog before writing CSV to storage or sharing. | P0 |
| **ANA-05** | CSV Accuracy | Exported CSV contains correct columns (`Date`, `Merchant`, `Bank`, `Account`, `Type`, `Amount`, `Category`, `Notes`). | P1 |

---

### Suite 9: Security, Privacy & System Settings

| Test ID | Test Scenario | Expected Outcome | Priority |
| :--- | :--- | :--- | :--- |
| **SEC-01** | Biometric App Lock | When enabled, moving app to background and foregrounding prompts Biometric prompt (Fingerprint/Face/PIN). | P0 |
| **SEC-02** | Screenshot Protection | When enabled in Settings, Android system prevents taking screenshots or showing content in Recent Apps. | P1 |
| **SEC-03** | Dark Mode Compatibility | App UI looks cohesive across light and dark system themes; text contrast adheres to Material 3 standards. | P2 |
| **SEC-04** | Inter Font Typography | Typography displays consistently across all screens without clipped text or broken line wrapping. | P2 |

---

## 3. High-Priority Negative & Edge Cases

| Area | Edge Case | Expected System Behavior |
| :--- | :--- | :--- |
| **SMS Ingestion** | SMS received in foreign language or mixed script | Fallback gracefully; extract numeric amount and sender code if available; do not crash. |
| **SMS Ingestion** | Multiple transactions in single SMS (e.g., split payment) | Correctly parses primary debit or logs split amounts cleanly. |
| **Network** | Device in Airplane mode during foreign currency view | Successfully resolves via offline historical fallback table; zero network errors shown. |
| **Storage** | Device running low on internal storage | SQLite Room operations handle `SQLiteFullException` cleanly with user warning. |
| **System** | Process death / Low memory kill during historical scan | Foreground Service / WorkManager resumes or reports graceful partial scan state. |
| **Data Integrity** | User edits an auto-parsed transaction category, then rescans SMS | Auto-rescan detects existing transaction ID / hash and **preserves** user's manual category edit. |

---

## 4. Test Environment & Matrix

### Device Coverage Matrix
- **Target OS Versions:** Android 8.0 (API 26) through Android 15 (API 35).
- **Recommended Test Devices:**
  - **Samsung Galaxy (OneUI 5/6)** — test background SMS reception restrictions & battery optimization.
  - **Google Pixel (Stock Android 14/15)** — test Material 3 dynamic themes and BiometricPrompt.
  - **Xiaomi / Redmi (MIUI / HyperOS)** — test auto-start permissions for SMS receivers.
  - **OnePlus / Realme (OxygenOS)** — test SMS permission flow and deep sleep behavior.

---

## 5. QA Sign-Off Criteria for Release

Before any build (e.g., `v1.1.3` / `v1.1.4`) is approved for production release on Google Play:

- [ ] **Zero P0 / Crash bugs:** No unhandled exceptions on app launch, SMS scan, or currency conversion.
- [ ] **Privacy Verification:** Packet inspection (e.g., via Charles / Wireshark) confirms zero SMS content or transaction details are transmitted externally.
- [ ] **Parser Accuracy Threshold:** ≥ 98% accuracy on standard test SMS suite across top 10 Indian banks.
- [ ] **Offline Resilience:** All features (except real-time FX rate refresh) functional with Wi-Fi and Data disabled.
- [ ] **UI Integrity:** All dialogs, calendar pickers, and filter chips display properly on both standard (1080p) and compact screen sizes.
- [ ] **Play Store Compliance:** App bundle passes Google Play Developer Policy checks for SMS permission use-case justification.
