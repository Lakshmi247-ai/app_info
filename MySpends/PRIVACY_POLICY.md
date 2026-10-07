# Privacy Policy for MySpends

**Effective Date:** October 1, 2026  
**Last Updated:** October 1, 2026  
**Application Name:** MySpends (`com.orbit.myspends`)  
**Website / Web Policy:** [https://lakshmi247-ai.github.io/MySpends/](https://lakshmi247-ai.github.io/MySpends/)

MySpends ("we", "our", or "us") is committed to protecting your privacy. This Privacy Policy explains how your information is handled when you use our mobile application **MySpends** available on Google Play.

Please read this Privacy Policy carefully. If you do not agree with the terms of this Privacy Policy, please do not access or use the application.

---

## 1. Core Privacy Philosophy: 100% Offline & Zero Data Sales

MySpends is built from the ground up as a **100% offline, privacy-focused financial management application**. 

- **Local Storage & Processing:** All transaction data, parsed bank SMS messages, category spending, sub-categories, bank accounts, and financial analytics are processed and stored **100% locally on your device** in a private, sandboxed SQLite Room database.
- **Zero Third-Party Trackers or Ad Networks:** We do not track your activity, use advertising IDs (no Google AdMob or ad SDKs), or sell/monetize your financial data to any third party.
- **No Remote Financial Servers:** MySpends does not transmit your financial transactions, account balances, or personal identity to external cloud servers or databases.
- **No User Account Required:** You do not need to register, submit an email address, provide phone numbers, or create an online profile to use MySpends.

---

## 2. Information Collected and Permissions Requested

### A. SMS Permissions (`READ_SMS` / `RECEIVE_SMS`)
- **Purpose:** MySpends reads bank and financial SMS notifications on your device to automatically detect debits, credits, account balances, and UPI transfers, saving you from manual entry.
- **Scope & Strict Safeguards:** 
  - Only financial SMS messages from recognized financial institutions (e.g., HDFC, ICICI, SBI, Axis, Kotak, Union Bank, and authorized UPI handles) are parsed.
  - Personal SMS messages (chats, two-factor OTPs, verification codes, and unrelated SMS notifications) are strictly ignored and never recorded.
  - **Parsing is 100% offline.** SMS content never leaves your physical device under any circumstances.

### B. Biometric Authentication (`USE_BIOMETRIC`)
- **Purpose:** Used for optional app lock security (Fingerprint / Face Unlock).
- **Privacy:** Handled entirely by your Android system's native `BiometricPrompt` hardware framework. MySpends never accesses or stores your raw biometric templates.

### C. Push Notifications (`POST_NOTIFICATIONS`)
- **Purpose:** Delivering local budget exceeded warnings, spend summaries, and daily financial wisdom reminders.
- **Privacy:** Handled entirely via on-device `WorkManager` background workers. No remote push notification tracking servers are used.

### D. Internet Access (`INTERNET` / `ACCESS_NETWORK_STATE`)
- **Transparency Disclosure:** MySpends accesses the internet solely for client-side utility queries:
  1. Fetching public, non-authenticated currency conversion rates (e.g., USD/EUR to INR).
  2. Loading public brand icons/logos from public CDNs.
- **Guarantee:** No financial data, account numbers, SMS messages, or personal identifiers are ever transmitted across the network.

### E. Approximate Location (`ACCESS_COARSE_LOCATION` - Optional)
- **Purpose:** Optional geotagging for manual transactions to populate the interactive spending heatmap. Location data remains entirely on-device and is never shared.

---

## 3. Data Security & Retention

- **Local Storage:** All financial records are stored locally in your app's private sandboxed data directory (`/data/data/com.orbit.myspends/`).
- **Data Retention:** Your data remains on your device for as long as the app is installed.
- **Data Export & Deletion Rights:**
  - You can export your data to CSV format at any time from the Analytics tab.
  - You can delete any individual transaction or clear bank records from the Settings screen.
  - You can uninstall the application or clear app data via Android Settings at any time to permanently and immediately erase all locally stored data.

---

## 4. Third-Party Services & Advertising

- **No Ads or Trackers:** MySpends does not use third-party ad networks, social login SDKs, or analytics trackers (such as Google Analytics or Firebase Crashlytics).
- **No Data Sharing:** We do not share, rent, or sell any user information to third parties.

---

## 5. Children's Privacy

MySpends is a financial tool directed to individuals aged 18 and older. We do not knowingly collect personal information from children under 18.

---

## 6. Google Play Policy Compliance

MySpends strictly complies with Google Play's User Data policy and Financial SMS policy by ensuring SMS permissions are used solely for the core expense tracking functionality and that all parsing operations happen 100% on-device.

---

## 7. Changes to This Privacy Policy

We may update our Privacy Policy from time to time. We will notify you of any changes by posting the new Privacy Policy in the app repository and updating the "Effective Date" at the top of this document.

---

## 8. Contact Us

If you have any questions or suggestions about our Privacy Policy, please contact us at:

- **Developer / Team:** MySpends Team
- **Support Email:** [teamorbit2000@gmail.com](mailto:teamorbit2000@gmail.com)
- **GitHub Repository:** [https://github.com/Lakshmi247-ai/MySpends](https://github.com/Lakshmi247-ai/MySpends)
