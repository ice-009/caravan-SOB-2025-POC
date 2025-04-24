## Elements to be added

- Brief description of what the Wallet Health Dashboard is.

- Purpose: Analyzing multisig wallet health using Caravan.

- Highlights: Scorecards, privacy and fee analysis, UTXO vis ualization.

### Features

- Fee Health Tab

Displays fee scorecards, fee-rate history chart, and UTXO dust analysis.

- Privacy Health Tab

Privacy category breakdown (donut chart), address reuse, and stats.

- UTXO Explorer Tab

Treemap of UTXOs, privacy vs. waste scatter plot, balance overview.

- Transaction Preview Panel

Evaluates unsigned PSBTs with privacy/waste feedback.

- Reusable Components

Scorecards, breakdown panels, visual charts using D3/Recharts.

### Project Structure

![](../images/struct-2.png)
![Waste Metrices](../images/comp_struct.png)

- useWalletHealth.ts: Central logic & metric calculations.

- FeeHealthTab.tsx, PrivacyHealthTab.tsx, UtxoExplorerTab.tsx: Main UI tabs.

- HealthScoreCard.tsx, MetricBreakdownPanel.tsx: Reusable UI components.

- UtxoTreemap.tsx, PrivacyVsWasteChart.tsx: Visualizations.
