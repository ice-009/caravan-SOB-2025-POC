# HealthScoreCard

![Heealth ScoreCard](../images/healthscorecard.png)

## Overview

A good dashboard doesn't just display data—it makes sense of it. In a domain as nuanced and technically complex as Bitcoin wallet management, users need more than raw numbers; they need **intuitive, meaningful summaries** of their wallet's health.

The `HealthScoreCard` component takes complex calculations around privacy, fee efficiency, UTXO management, and waste—and translates them into clear, visually engaging metrics. It serves as a modular and reusable visual block that empowers users to track their wallet's status over time and understand which aspects of their behavior or transaction history need attention.

![HealthScoreCard example](https://via.placeholder.com/600x200)

## Features

- **Surface key scores** in a compact and readable form
- **Color-coded insights** (green, yellow, red) to immediately signal health levels
- **Progress bars/gauges** to indicate metric range (0–1 scale or satoshis)
- **Informative tooltips** to educate users on what each score means
- **Support for multiple score types** (numeric values, ratios, percentages)
- **Reusable** across multiple sections of the dashboard

## Usage

```jsx
import { HealthScoreCard } from "@wallet/health-score-card";

function FeeHealthTab() {
  // Score from your wallet health hook
  const { feeWasteScore } = useWalletHealth();

  return (
    <div className="dashboard-section">
      <HealthScoreCard
        title="Fee Waste Score"
        score={feeWasteScore}
        tooltip="Measures how efficiently your wallet selects inputs for transactions"
        unit="score" // or "sats" or "percentage"
        colorScale="inverse" // lower values are better (green), higher values are worse (red)
        learnMoreUrl="/docs/fee-waste-score"
      />
    </div>
  );
}
```

## Props

| Prop           | Type                              | Default    | Description                                                |
| -------------- | --------------------------------- | ---------- | ---------------------------------------------------------- |
| `title`        | string                            | Required   | Short title for the metric                                 |
| `score`        | number                            | Required   | The numeric score (typically 0-1)                          |
| `tooltip`      | string                            | null       | Explanation text for the score                             |
| `unit`         | "score" \| "sats" \| "percentage" | "score"    | Unit type for display formatting                           |
| `colorScale`   | "standard" \| "inverse"           | "standard" | Whether high (standard) or low (inverse) values are better |
| `learnMoreUrl` | string                            | null       | Optional URL to more documentation                         |
| `size`         | "sm" \| "md" \| "lg"              | "md"       | Size variant of the card                                   |
| `showBadge`    | boolean                           | true       | Whether to show a color-coded badge                        |
| `showBar`      | boolean                           | true       | Whether to show the progress bar                           |

## Architecture Integration

The `HealthScoreCard` is used widely across the following tabs:

- **FeeHealthTab**: To represent waste score, fee rate health, and dust ratio
- **PrivacyHealthTab**: For privacy entropy, address reuse factor, and change type diversity
- **UtxoExplorerTab**: For UTXO quality score, cluster consistency, and overall wallet health

It consumes props from the central state hook `useWalletHealth`, where pre-calculated scores are fetched, normalized, and passed to this component for display.

## Component Structure

The `HealthScoreCard` is structured into:

1. **Title Area**

   - Displays a short title like "Privacy Score" or "Fee Efficiency"
   - Includes a small tooltip or info icon to expand on what the score means

2. **Main Score Display**

   - Either a raw numeric score (e.g., 0.87) or a sats amount (e.g., 800 sats wasted)
   - Large, readable font with emphasis on precision

3. **Progress Bar / Gauge**

   - Visual representation of the score as a bar or gauge that moves between 0 and 1 (or another range)
   - Background color gradient: green (good) → yellow (moderate) → red (critical)

4. **Color Indicator / Badge**

   - Small visual badge/icon with color coding to reinforce the severity of the score

5. **Score Explanation Tooltip**
   - When hovered or clicked, shows a quick explanation with optional links to "Learn More"

## Design Considerations

The design is focused on clarity and minimalism:

- **Rounded card layout** with light shadows for hierarchy
- **Tailwind CSS** or Shadcn components for responsive layout
- **Accessible color palette** for colorblind users
- **Mobile-responsiveness** for small screen use
