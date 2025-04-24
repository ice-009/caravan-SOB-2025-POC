# Interactive Visualizations with D3 and Recharts

## 📌 Overview

In any data-driven application, raw numbers alone are rarely enough to inform meaningful decisions. Visual representations bridge the gap between technical metrics and intuitive understanding. In the Wallet Health Dashboard project, one of the most important goals is to **empower users with interactive, visually rich insights** into their multisig wallet health. This is achieved through thoughtfully designed visualizations that are both **informative** and **interactive**, allowing users to explore their UTXOs, privacy patterns, and transaction efficiency.

To accomplish this, we integrate a mix of **D3.js** for custom low-level visualizations and **Recharts**, a high-level charting library built on React and D3, for rapid yet flexible UI development. These tools provide the perfect blend of performance, interactivity, and React compatibility needed for this project.

## 🎯 Goals of Visualization in the Dashboard

* **Simplify complexity**: Show users what's going on in their wallets in a form they can easily grasp.
* **Identify patterns and anomalies**: Make it obvious when there are issues, such as too many dust UTXOs or reused addresses.
* **Enable data-driven decisions**: Let users visually simulate the impact of actions like spending a UTXO or crafting a new PSBT.
* **Offer high interactivity**: Charts should respond to user actions—hovering, clicking, zooming, filtering.

## 🧰 Tools Used

| Tool | Purpose |
|------|---------|
| **D3.js** | For custom, low-level SVG visualizations (e.g., UTXO treemaps). |
| **Recharts** | For bar charts, scatter plots, and donut charts with quick development and rich React integration. |
| **React + Hooks** | For managing interactivity and real-time updates in response to wallet state. |
| **Responsive Containers** | For ensuring visuals work across devices and screen sizes. |

## 🔍 Major Visualizations

Here are the key visual components developed and their roles:

### 1. **UTXO Treemap (D3-based)**

* **Purpose**: Provides a spatial representation of the wallet's current UTXO set, grouped by size or address type.
* **Design**: Each UTXO is shown as a rectangle, with area proportional to its value.
* **Interactivity**:
   * **Hover** to show details: value, type, cluster ID.
   * **Click** to isolate or highlight reused/spent/dust UTXOs.
   * **Color-coding** to indicate UTXO hygiene—green for clean, red for reused, gray for uneconomical.

This visualization is invaluable for power users managing dozens or hundreds of UTXOs, especially for visualizing inefficiencies due to fragmentation or reuse.

### 2. **Privacy vs. Waste Scatter Plot (Recharts)**

* **Purpose**: Shows the trade-off between privacy and fee efficiency across historical transactions.
* **Design**:
   * **X-axis**: privacy score (e.g., cluster entropy, address diversity).
   * **Y-axis**: waste score (e.g., overpayment due to too many inputs or dust).
   * **Each dot** represents one past transaction.
   * **Tooltip on hover** shows detailed stats about the selected transaction.
* **Insights**:
   * Top-left quadrant = high privacy, low waste (ideal).
   * Bottom-right = poor privacy, high waste (problematic).
   * Allows users to learn from past mistakes and successes.

### 3. **Donut Chart for Privacy Breakdown (Recharts)**

* **Purpose**: Categorizes transactions into buckets based on privacy level: Perfect, Adequate, Poor.
* **Usage**: Part of the `PrivacyHealthTab`, this chart gives a quick overview of the privacy landscape.
* **Interactivity**:
   * **Slice hover** shows the exact number and percentage of transactions in each category.
   * **Click** redirects or filters the transaction table to show only selected category.

This visualization helps less-technical users grasp how private their wallet truly is without reading through logs or lists.

### 4. **Fee Rate History Chart (Line Chart in Recharts)**

* **Purpose**: Tracks the wallet's average fee rate over time, overlaid with mempool data if available.
* **Insights**:
   * Helps users see when they overpaid or underpaid compared to network congestion.
   * Can surface patterns like consistent overpayment or missing priority fee windows.

### 5. **Clustered Address Usage Table with Highlights**

While technically not a chart, this component visually presents data using **conditional styling and sparklines** to show address behavior, reuse frequency, and impact. Rows light up based on severity, encouraging immediate corrective action.

## 🧠 Data Preparation

Each visualization relies on carefully structured and preprocessed data, handled by the central `useWalletHealth` hook. For example:

* UTXO treemaps require hierarchical clustering by value, address type, and status.
* Scatter plots need normalization of score values.
* Donut charts categorize transactions based on multiple computed dimensions (ARF, ATF, etc.).

This approach ensures visual components are **dumb renderers**: they don't fetch or compute—they just draw, enhancing performance and reusability.

## 💡 Design Principles

* **Consistent Theming**: Visuals use consistent color palettes (e.g., green-yellow-red gradient for scores).
* **Responsiveness**: All charts adapt to screen size using `ResponsiveContainer` wrappers.
* **Tooltips and Guidance**: Every chart includes contextual tooltips or legends, reducing learning curve.
* **Accessibility**: Colors are chosen with colorblind safety in mind; legends are also labeled with text.

## 🔄 Dynamic Updates

These visualizations update in real-time whenever:

* The wallet state changes (e.g., new PSBT, new block).
* The user interacts (e.g., filters a transaction category).
* The user toggles advanced settings (e.g., privacy mode on/off).

Thanks to React hooks and component state, the interface feels live and reactive—key for trust and usability.

