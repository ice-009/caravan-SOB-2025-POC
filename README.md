# caravan-SOB-2025-POC
# Project Overview

This repository contains my proposal and initial plans to integrate a Wallet Health Dashboard into the Caravan multisig coordinator. The main objective is to empower users with tools to assess the health of their Bitcoin wallets—focusing on privacy and waste—using real-time analysis of transaction drafts and historical wallet activity. This will bridge the gap between existing backend analysis (via the @caravan/health package) and a clean, intuitive UI experience, making advanced wallet insights accessible to all Caravan users.

# Why This Matters
Caravan makes multisig Bitcoin setups more accessible, but understanding the privacy and efficiency of your wallet often requires deep domain knowledge. Without visual, actionable feedback, users may unknowingly compromise on privacy or waste funds due to suboptimal coin selection strategies.

This project addresses that by:

Surfacing meaningful insights about transaction structures before signing or publishing.

Helping users understand tradeoffs between privacy and waste.

Building intuitive dashboards that educate users on best practices in Bitcoin wallet management.

The result will be a more secure, informed, and user-friendly Caravan experience.

# What You’ll Find in This Repository
The repo is organized to give a clear picture of both the research and the implementation process:

- [**`docs/`**](./docs): Technical deep dives

  - [`overview.md`](./docs/overview.md): This document explains how the wallet-activity generator works under the hood, providing insights into the simulation of wallet activity.

  - [`adding-elements.md`](./docs/implementation-details.md): Step-by-step guide to building new components

- [**`images/`**](./images/): Diagrams and screenshots used in documentation

- [**`implementations/`**](./implementations/README.md): Working prototypes

  - Core Components and Features of the Wallet Health Dashboard
  - Key Implementations: Wallet Simulator, Health API, and UI Components

- [**`suggestions/`**](./suggestions/README.md): Future features and ideas
  - "Interactive Component Explorer" - Develop a visual tool that allows users to explore and understand how different wallet components work.
  - A drag-and-drop Visual Component Creator!



# How I Approached This
The project was broken down into three key phases:

## 1. Understanding Caravan Internals & Wallet Health Metrics
- I began with an in-depth study of the Caravan Coordinator and the @caravan/health library to understand:

- How Caravan handles multisig wallet creation and transaction signing

- How the WalletMetrics, PrivacyMetrics, and WasteMetrics classes are structured

- What types of privacy and fee-efficiency analyses are already supported

- How to simulate and inspect wallet behavior using regtest/signet environments


## 2. Building the Core Tools & Simulations
- With that knowledge, I started building a development environment and initial tools:

- Wallet Activity Simulator: A script to generate a wallet with varied UTXO patterns using regtest/signet, useful for triggering different health scores.

- Transaction Analysis Panel: UI component that evaluates draft transactions before signing, showing privacy and waste implications.

- Health Dashboard UI: Prototype React components that surface insights from the metrics classes in real time.

- UTXO Visualizer: A D3.js-based treemap for visualizing clustering, address reuse, and dust.


## 3. Looking Ahead — Enhancement Ideas
- Once the foundation was in place, I explored ideas to make Caravan more transparent and user-friendly for both technical and non-technical users:

- Health Scoring Summary: A scorecard with aggregate Privacy and Waste scores, backed by drill-down views.

- Address & UTXO Explorer: An interactive view to understand address reuse, dust UTXOs, and script types at a glance.

- Transaction Topology Insight: Classify transactions in real-time (e.g., consolidation, CoinJoin-like, self-payments) and explain their privacy impact.

- Modular Metrics Framework: Build a plug-in-ready system where contributors can add new analysis types (e.g., spending patterns, cluster detection) without touching core logic.


# What’s Next?

If selected, my plan is to:

- Finalize, test, and polish all core components of the Wallet Health Dashboard
- Build a wallet activity simulator to create rich UTXO patterns for testing privacy/waste scores
- Write in-depth documentation and tutorials to explain metrics to both users and developers
- Design clean, intuitive visualizations (e.g., charts, scorecards, treemaps) using D3.js
- Iterate on the UI/UX based on community feedback to make it approachable for non-technical users
- Implement and release the dashboard incrementally to reduce review burden and ensure stable rollouts



