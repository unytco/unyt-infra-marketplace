# Unyt Infrastructure Marketplace

A Unyt application demonstrating **unit bridging and mirroring** between independent accounting networks. This Infrastructure Marketplace Unyt Application is one of two interconnected Unyt Apps released as part of the January 30th, 2026 release and is designed to work alongside the **[Holo Hosting](https://github.com/unytco/holo-hosting)** Unyt application.

> Built on [Holochain](https://holochain.org) 0.6.1-rc with Iroh networking

## Overview

Infrastructure Marketplace enables peer-to-peer transactions across multiple monetary currencies (**HoloFuel** and **Spore**) while supporting seamless transfers to and from external Unyt networks. This release marks a major milestone: **the first Unyt implementation of cross-application bridging** using smart agreements that bind internal "mirrored" units to the locking of base units in another network.

### What This Release Demonstrates

This application is purpose-built to demonstrate two capabilities:

1. **Unit Bridging & Mirroring** — Establish a bridging agent with smart agreements that bind internal "mirrored" units of account to "base" units locked in some manner in another digitally signed accounting framework. This release demonstrates interoperability between Unyt Apps using locking by sending to a Unyt Smart Agreement. Future releases will support bridging between Unyt Apps and Blockchains, and, eventually, to TradFi sources with digital signature support.

2. **Bi-Directional Multi-Unit Payments** — Complete single transaction trades involving multiple payment units flowing in both directions. This goes *beyond trading pairs*, enabling true currency market transactions where parties can exchange multiple currencies simultaneously.

## How Bridging Works

Cross-network transfers operate through a coordinated bridging mechanism using **smart agreements** and Bridge Agents. A smart agreement defines the rules, roles, and unit flows for a transaction.

![Unit Bridging Architecture](docs/images/hf_20260130_031959_cedf59a5-ef09-4d8d-8292-b26d8b743411%20(1).png)

### Transfer Out: Holo Hosting → Infrastructure Marketplace

Let's say Alice has HoloFuel (HF) in the Holo Hosting app and wants to transfer 100 HF to her account in Infrastructure Marketplace.

**Step 1: Initiate Transfer Out (in Holo Hosting)**
- Alice opens **Holo Hosting** and navigates to **Transfer Out**
- She selects **HoloFuel** as the unit and **Infrastructure Marketplace** as the destination network
- She enters her **Address from Infrastructure Marketplace** as the destination address
  > 💡 This is where external contacts are created — you must create a contact with the destination address from the Transfer Out workflow. At present, you cannot add external network addresses through the regular address book

**Step 2: Submit to the Holo Hosting Bridging Smart Agreement**
- Alice enters the amount (100 HF) and confirms the transfer
- The transaction is submitted to the **bridging smart agreement** on the Holo Hosting network
- 100 HF is debited from Alice's Holo Hosting account and that payment is attached to the Unyt Holo Hosting Bridging Smart Agreement.

**Step 3: Bridging Agent Coordination**
- The **bridging agent** on Holo Hosting gets notified of the action (or checks on an interval basis) and executes the Bridging Smart Agreement
- The Unyt Holo Hosting Bridging Agent communicates the transfer details (amount, destination address, source verification) to its counterpart bridging agent on Infrastructure Marketplace

**Step 4: Transfer the Mirrored Unit**
- The bridging agent on **Unyt Infrastructure Marketplace** receives the transfer notification
- It executes the corresponding Smart Agreement on the Infrastructure Marketplace side which adjusts the Bridging Agent's credit limit by 100 HF.
- Alice's Unyt Infrastructure Marketplace account is credited with **100 HF** (a mirrored version of the locked Base HF units)

**Step 5: Confirmation**
- Alice sees the incoming transfer appear in her Infrastructure Marketplace inbox
- She accepts the transaction, and her balance now shows +100 HF

*Since this is really just accounting, technically, the HF units in the Infrastructure Marketplace App are Mirrored HF, but since the other units are locked and these are 1:1, it is simpler to just refer to them also as HF.

---

### Transfer In: Infrastructure Marketplace → Holo Hosting

Now Alice wants to send 50 HF (-mirror) back from Unyt Infrastructure Marketplace to Holo Hosting.

**Step 1: Initiate Transfer In**
- Alice opens **Unyt Infrastructure Marketplace** and navigates to **Transfer Out**
- She selects **HF** as the unit and **Holo Hosting** as the destination network
- She enters her **Address from Holo Hosting** as the destination address

**Step 2: Submit to the Infra Bridging Smart Agreement**
- Alice enters the amount (50 HF) and confirms
- Alice's Unyt Infrastructure Marketplace account gets debited 50 HF
- The 50 HF are attached to the Unyt Infra Bridging Smart Agreement

**Step 3: Bridging Agent Coordination**
- The bridging agent on Unyt Infrastructure Marketplace executes the Smart Agreement
- It then signals the Unyt Holo Hosting bridging agent with the verified transfer details

**Step 4: Release from Reserve**
- The bridging agent on **Unyt Holo Hosting** executes the return smart agreement
- 50 HF is released from the Smart Agreement and sent to Alice's Unyt Holo Hosting account

**Step 5: Confirmation**
- Alice sees the incoming transfer in her Unyt Holo Hosting inbox
- Upon acceptance, her Unyt Holo Hosting balance reflects +50 HF

**Note:** Rules and constraints can be put in place to control the flow of transactions across any particular bridge. Those are determined by the Bridging Smart Agreements on either side of a bridge.

---

> ⚠️ **Contacts for external networks must be created during the Transfer In or Transfer Out process.** The standard address book only supports adding contacts within the same Unyt network. When you initiate a cross-network transfer, you'll be prompted to select one that you have previously saved or enter and save the destination address. This is the only way to establish a contact on an external network.

## Features from Recent Releases

### Unit Type System

Unyt now supports five distinct unit types, each with specialized validation and accounting behaviors:

| Type | Description | Credit Limits | Use Case |
|------|-------------|---------------|----------|
| **Monetary** | Fungible monetary units with variable precision | ✓ | Currency, credits |
| **Tradable** | Embodied units with subtypes | ✓ | Inventory, assets |
| **Measure** | Measurable units (meters, kilograms) | ✗ | Measurements transmitted, not transferred |
| **Rating & Ranking** | Units with min/max value ranges | ✗ | Ratings, scores |
| **Reputation & Relation** | Units mapping to dictionary entries | ✗ | Categorical data |

### Multi-Unit Transactions

Zome call APIs now support multi-unit transactions with lane and global definitions:
- `create_spend`, `accept_transaction`, `create_parked_spend` accept optional lane definitions
- `execute_rave` requires global definitions with optional lane definitions
- Enables complex transactions across multiple currencies in a single atomic operation

### Smart Agreements with Permissions

Smart agreements, lanes, and code templates now include **permission spaces** (Global, Lane, Default) for fine-grained access control during creation and updates.

## Getting Started

### System Requirements

| Component | Requirement |
|-----------|-------------|
| **macOS** | Ventura (13.x) or later, Intel or ARM (M1/M2/M3) |
| **Linux** | Ubuntu 20.04+, Debian-based distributions |
| **RAM** | Minimum 4GB |
| **CPU** | 2-core minimum |
| **Storage** | 1GB free space |
| **Network** | Stable connection (50ms latency, 10Mbps bandwidth recommended) |

### Installation

Download the latest release for your platform from the [releases page](https://github.com/unytco/unyt-infra-marketplace/releases).

**Note for Windows users:** Use the `.exe` installer rather than `.msi` for this release.

### First Launch

1. Launch Infrastructure Marketplace
2. Note your **Agent Key** (Address) — this is your unique identifier on the network
3. Optionally add your name with a note identifying this app instance (e.g., "Alice, Infra")
4. Wait for network sync (may take several seconds to several minutes)

### Testing with Holo Hosting

To test bridging functionality, you'll need both applications:
- **Infrastructure Marketplace** (this repo) — v0.1.0
- **[Holo Hosting](https://github.com/unytco/holo-hosting)** — Unyt v0.49.0

> ⚠️ **Important:** Addresses are not transferable between networks. Each application instance generates a unique agent key.

## Core Actions

### Pay & Request
Send or request payments in HoloFuel or Spore currencies.

### Trade
Propose multi-unit exchanges with amounts to debit and credit across different currencies.

### Transfer Out
Move units from Infrastructure Marketplace to another Unyt network (e.g., Holo Hosting).

### Transfer In
Receive units bridged from an external Unyt network.

### Accept & Reject
Process incoming transactions from your inbox.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| App not responding | Right-click → Reload |
| Persistent errors | Quit and restart the application |
| Sync issues | Wait—most sync issues resolve within a few minutes |
| Transaction stuck | Wait 10-15 seconds for network recovery |

**Error Reporting:** This application uses [Sentry.io](https://sentry.io) for error tracking. Detailed errors can be viewed via the browser console (Right-click → Inspect → Console) or copied to clipboard using the copy icon.

## Architecture

This is a [Tauri](https://tauri.app) application wrapping a Holochain conductor:

- **Frontend:** White-label Unyt UI (`unyt/ui/white-label/dist`)
- **Backend:** Holochain 0.6.1-rc with Iroh networking
- **Security:** Argon2 password hashing with OS Keychain salt storage

## For Developers

See the [Unyt Sandbox Repository](https://github.com/unytco/unyt-sandbox) for a general overview of the Unyt framework.

### Build Requirements

- Rust 1.70+
- Node.js and Yarn
- Nix (optional, for reproducible builds)

### Development

```bash
# Build the UI
cd unyt && make ci-build-ui

# Run in development mode
cargo tauri dev
```

## Learn More

- **[Beyond Trading Pairs](https://unyt.co/blog/beyond-trading-pairs-bi-directional-multi-unit-payments/)** — Unyt's bi-directional multi-unit payment system enabling currency market transactions that transcend traditional trading pairs
- **[Unit Bridging & Mirroring](https://unyt.co/blog/unit-bridging-and-mirroring/)** — Smart agreements binding mirrored units to external reserves across accounting frameworks
- **[Unyt 2025 & 2026: Revolutionary P2P Payment Rails](https://unyt.co/blog/unyt-2025-and-2026:-revolutionary-p2p-payment-rails/)** — Overview of Unyt's vision for generalized tooling for value tracking

Visit [unyt.co](https://unyt.co) for more blog posts and documentation.

## License

This project is licensed under the terms specified in the [LICENSE](LICENSE) file.

Copyright (C) 2024 - 2026, unyt.co
