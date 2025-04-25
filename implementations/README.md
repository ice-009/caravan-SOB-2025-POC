# 🧠 Wallet Health Dashboard - GSoC 2025 Project

The **Wallet Health Dashboard** is a comprehensive analytical interface for Bitcoin multisig wallets (Caravan-based) aimed at improving users’ understanding of their wallet’s performance, privacy, fee optimization, and overall usability. This tool fetches raw wallet data, runs metrics and visualization algorithms, and presents them in an intuitive interface.

### 🚀 What This Project Implements

This SoB project contributes 5 major components to enhance wallet health transparency:

---

### 1. **Core Wallet Health Hook (`useWalletHealth.ts`)**
- 📌 A central logic hook that integrates with the wallet API to fetch UTXO and transaction data.
- 📈 Processes wallet scores and metrics for other dashboard components.
- 🔗 [Read full breakdown →](./health_score_engine.md)

---

### 2. **Fee Health Overview (`FeeHealthTab.tsx`)**
- 💰 Visualizes historical fee rates and dust UTXOs.
- 📉 Detects inefficient UTXOs and estimates fees.
- 🔗 [Read full breakdown →](./modular_dash.md)

---

### 3. **Privacy Health Panel (`PrivacyHealthTab.tsx`)**
- 🔐 Shows address reuse and privacy scores using donut charts and metric cards.
- 🕵️ Includes breakdown of privacy leaks with tables and visualizations.
- 🔗 [Read full breakdown →](./presign.md)

---

### 4. **UTXO Explorer Visualizations (`UtxoExplorerTab.tsx`)**
- 🌐 Treemap + scatter plot of UTXOs for better coin management.
- 📊 Includes balance summaries, waste estimations, and fee scorecards.
- 🔗 [Read full breakdown →](./recharts.md)

---

### 5. **Reusable Metric Score Components**
- 🧮 `HealthScoreCard.tsx`, `MetricBreakdownPanel.tsx`, and more.
- ♻️ Reusable components to represent scores like ARF, ATF, USF, and Privacy.
- 🔗 [Read full breakdown →](./health-scorecard.md)

---

