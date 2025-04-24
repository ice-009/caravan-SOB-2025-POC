# Caravan Health

A toolkit for analyzing and improving the health of Bitcoin wallets, focusing on privacy and fee efficiency metrics.

## 📊 Overview

Caravan Health helps you evaluate the "health" of Bitcoin wallets through two key dimensions:

1. **Privacy** - How well your transaction history protects your financial privacy
2. **Waste** - How efficiently you're managing transaction fees and UTXOs

This package works alongside the Caravan Bitcoin multisig coordinator to provide real-time feedback on wallet health.

## 🚀 Features

### Privacy Analysis
- Transaction topology scoring
- Address reuse detection
- Address type diversity analysis
- UTXO distribution assessment

### Fee Efficiency Analysis
- Spend waste calculation
- Fee rate comparison
- UTXO economic viability
- Dust limit detection

### Visualization
- Health score dashboards
- UTXO explorer with clustering visualization
- Transaction analysis panel


## 📋 Usage

### Basic Usage

```typescript
import { PrivacyMetrics, WasteMetrics } from '@caravan/health';

// Initialize with transactions and UTXOs
const privacyMetrics = new PrivacyMetrics(transactions, utxos);
const wasteMetrics = new WasteMetrics(transactions, utxos);

// Calculate wallet health scores
const privacyScore = privacyMetrics.getWalletPrivacyScore('p2wsh', 'mainnet');
const wasteScore = wasteMetrics.getWeightedWasteScore(feeRatePercentileHistory);

console.log(`Privacy Score: ${privacyScore}`);
console.log(`Waste Score: ${wasteScore}`);
```

### Transaction Analysis

```typescript
// Analyze a draft transaction before signing
const txAnalysis = privacyMetrics.analyzeTransaction(draftTransaction);

console.log(`Transaction Topology: ${txAnalysis.spendType}`);
console.log(`Privacy Score: ${txAnalysis.privacyScore}`);
console.log(`Potential Waste: ${wasteMetrics.spendWasteAmount(
  draftTransaction.weight,
  currentFeeRate,
  draftTransaction.inputSum,
  draftTransaction.outputSum,
  estimatedLongTermFeeRate
)}`);
```

### UTXO Management

```typescript
// Calculate dust limits for different address types
const dustLimits = wasteMetrics.calculateDustLimits(
  currentFeeRate,
  'p2wsh',
  { requiredSignerCount: 2, totalSignerCount: 3 }
);

console.log(`Lower Dust Limit: ${dustLimits.lowerLimit} sats`);
console.log(`Upper Safe Limit: ${dustLimits.upperLimit} sats`);
```

## 📊 Metrics Explanation

### Privacy Metrics

| Metric | Description | Range | Ideal Value |
|--------|-------------|-------|-------------|
| Transaction Topology Score | Privacy based on tx structure | 0-1 | Higher |
| Address Reuse Factor (ARF) | Funds in reused addresses | 0-1 | Lower |
| Address Type Factor (ATF) | Address type diversity | 0-1 | Lower |
| UTXO Spread Factor (USF) | Diversity of UTXO values | 0-1 | Higher |
| UTXO Value Dispersion Factor | Overall UTXO distribution | -0.15-0.15 | Higher |
| Wallet Privacy Score | Combined privacy rating | 0-1 | Higher |

### Waste Metrics

| Metric | Description | Range | Ideal Value |
|--------|-------------|-------|-------------|
| Spend Waste Amount (SWA) | Efficiency of spending now vs later | Any (sats) | Lower |
| Relative Fees Score (RFS) | Fee efficiency vs network | 0-1 | Higher |
| Fees to Amount Ratio (FAR) | Fees as % of transaction value | 0-1 | Lower |
| Weighted Waste Score | Combined fee efficiency rating | 0-1 | Higher |

## 📝 Usage Examples

### Improving Privacy

```typescript
// Get UTXO value distribution
const utxoDistribution = privacyMetrics.getUtxoDistributionStats();

// Find reused addresses
const reusedAddresses = privacyMetrics.getReusedAddresses();

// Suggest improvements
console.log(`Number of reused addresses: ${reusedAddresses.length}`);
console.log(`UTXO distribution standard deviation: ${utxoDistribution.standardDeviation}`);
```

### Optimizing Fee Efficiency

```typescript
// Get potential waste for consolidation transaction
const consolidationWaste = wasteMetrics.calculateConsolidationWaste(
  smallUtxos,
  currentFeeRate,
  estimatedLongTermFeeRate
);

// Determine optimal time to consolidate
const shouldConsolidate = consolidationWaste < 0;

console.log(`Consolidation waste: ${consolidationWaste} sats`);
console.log(`Recommended action: ${shouldConsolidate ? 'Consolidate now' : 'Wait for lower fees'}`);
```

# Fee‐Efficiency: WasteMetrics

This document describes the `WasteMetrics` class, designed to analyze the fee efficiency of wallet transactions based on historical data and transaction details.

The `WasteMetrics` class extends `WalletMetrics` and provides several methods to calculate fee-related efficiency scores.

```typescript
export class WasteMetrics extends WalletMetrics { 
  // ... methods described below ... 
}

## Metrics Overview
Here are the key metrics provided by the WasteMetrics class:

1. Relative Fees Score (RFS)
```typescript
relativeFeesScore(history: FeeRatePercentile[]): number {
  let sum = 0, count = 0;
  for (const tx of this.transactions) {
    if (tx.isSend) {
      count++;
      const rate = this.getFeeRateForTransaction(tx);
      sum += this.getFeeRatePercentileScore(tx.block_time, rate, history);
    }
  }
  return count ? sum/count : 0;
}
```
Description:

Calculates the Relative Fees Score (RFS). For each outgoing transaction, this function determines where the paid fee rate sat within the distribution of fee rates in the block it was confirmed in (based on historical FeeRatePercentile data). It then averages these percentile scores across all outgoing transactions to produce a final score between 0 and 1. A higher RFS suggests that the wallet consistently pays competitive fees relative to the network conditions at the time of each transaction.

2. Fees-to-Amount Ratio (FAR)
```typeScript

feesToAmountRatio(): number {
  let totalRatio = 0, count = 0;
  for (const tx of this.transactions) {
    if (tx.isSend) {
      // Note: Ensure tx.amount represents the intended spend amount, 
      // not the total output value which might include change.
      totalRatio += tx.fee / tx.amount; 
      count++;
    }
  }
  return count ? totalRatio/count : 0;
}
```
Description:

Measures the average ratio of the fee paid to the amount spent for outgoing transactions. This helps identify if transaction fees constitute a significant percentage (e.g., >1%) of the value being transferred, providing insight into the cost-effectiveness of spending operations.

3. Spend Waste Amount (SWA)
```typeScript
spendWasteAmount(
  weight: number,           // Weight of the UTXO being spent (e.g., its value in satoshis)
  feeRate: number,          // Current fee rate (e.g., satoshis per virtual byte)
  inputSum: number,         // Total value of inputs used in the transaction
  spendAmount: number,      // The actual amount intended to be sent (excluding change)
  estLongTermRate: number   // Estimated long-term average fee rate
): number {
  // Cost associated with creating change or not using the input optimally
  const cost = Math.abs(spendAmount - inputSum); 
  // Compares current fee rate cost vs long-term rate cost, scaled by UTXO weight
  // Adds the cost of suboptimal input selection (change creation)
  return weight * (feeRate - estLongTermRate) + cost;
}
```
Description:

Estimates the potential "waste" associated with spending a specific UTXO (or set of inputs) at the current fee rate compared to waiting for a potentially different future fee rate.

If the current feeRate is lower than the estLongTermRate, the first part of the calculation is negative, suggesting it might be beneficial to spend/consolidate now (potential negative SWA if cost is low).
If the current feeRate is higher, the first part is positive, indicating potential savings by waiting for lower fees (positive SWA).
The cost term adds waste incurred due to factors like creating change outputs when inputSum doesn't match spendAmount.
4. Dust Limits
```typeScript

calculateDustLimits(
  feeRate: number,                   // Current fee rate (e.g., satoshis per vbyte)
  scriptType: MultisigAddressType,   // Type of script (e.g., P2SH, P2WSH, P2TR)
  { requiredSignerCount, totalSignerCount }: { 
      requiredSignerCount: number; 
      totalSignerCount: number; 
  }, // Multisig parameters if applicable
  riskMultiplier = 2                 // Multiplier for the upper limit buffer
): { lowerLimit: number; upperLimit: number } {
  
  // --- Placeholder for actual vsize calculation ---
  // This part needs to compute the virtual size (vsize) required 
  // to spend an output of the given scriptType. 
  // This depends heavily on the specifics of the script (P2PKH, P2SH, P2WPKH, P2TR, etc.)
  // and potentially the multisig parameters.
  // Example: const vsize = estimateSpendingVsize(scriptType, { requiredSignerCount, totalSignerCount }); 
  let vsize = 100; // Replace with actual calculation based on scriptType
  // --- End Placeholder ---

  // The minimum value an output needs to have to be worth spending at the current fee rate.
  const lower = vsize * feeRate; 
  
  // An upper limit providing a buffer, based on risk tolerance.
  return { lowerLimit: lower, upperLimit: lower * riskMultiplier };
}
```
Description:

Computes the economic "dust" limits for a UTXO based on the current feeRate and the script type (which determines the cost to spend it).

lowerLimit: The minimum value (in satoshis) an output must have for the cost of spending it (fee) not to exceed its value. Outputs below this are generally considered uneconomical to spend at the given fee rate.
upperLimit: Provides a buffer above the strict dust limit, determined by the riskMultiplier. UTXOs between the lower and upper limits might be considered marginal depending on risk tolerance and future fee expectations.
5. Weighted Waste Score (WWS)
```typeScript
weightedWasteScore(history: FeeRatePercentile[]): number {
  const RFS = this.relativeFeesScore(history); // How competitive were your fees? (0-1)
  const FAR = this.feesToAmountRatio();       // How much did fees cost relative to sends? (Ratio)
  
  // Assumes a method `utxoMassFactor` exists, possibly measuring UTXO fragmentation or similar.
  // Needs definition - Placeholder value used.
  const UMF = this.utxoMassFactor ? this.utxoMassFactor() : 0.5; // Example: (0-1)

  // Weighted average combining different efficiency aspects.
  // Weights (0.35, 0.35, 0.3) should sum to 1.
  // Note: FAR is a ratio, not inherently 0-1. It might need normalization 
  // or careful interpretation in this weighted sum.
  // Consider clamping or transforming FAR if necessary.
  return 0.35 * RFS + 0.35 * Math.min(FAR, 1) + 0.3 * UMF; // Example with FAR clamped
}
```


