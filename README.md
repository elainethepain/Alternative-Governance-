
# Civic Coordination Protocol

This protocol provides a set of smart-contract programs and supporting libraries for communities that want to run a universal basic income (UBI) system with transparent governance.

Built on Solana using Anchor, the system combines trust-based identity verification with programmable governance and coordination mechanisms for digitally native communities and cooperative structures.

Modules provided include identity verification, councils, referenda, reputation graphs, civic participation incentives, UBI distribution, and community-directed resource allocation.

### Modules

| Module | Description |
| --- | --- |
| `lib/accounts/elections` | Account structures for governance systems including councils, voting periods, election cycles, and community preference tracking. |
| `lib/accounts/trust-networks` | Identity and trust infrastructure for managing verified participants, reputation relationships, wallet links, and trust-based participation rules. |
### Programs

- **`census`** – placeholder initialization for maintaining census information.
- **`elections`** – (imported as a submodule) implements council elections and preference pooling.
- **`referenda`** – records proposals and referendum outcomes.
- **`sampling`** – supports random sampling of members (e.g., for juries or committees).
- **`solana-ubi`** – mints and distributes UBI tokens on a schedule, integrating with the trust network to prevent duplicate claims.
- **`solana-fundraisers`** and **`solana-petition`** – allow fundraising campaigns and petition management.
- **`trust-networks`** – on-chain logic for building and updating the trust graph.

## Governance & elections

### Councils and election periods

The `Council` account defines a governance period with the following fields:

- `period` – identifies the election cycle.
- `period_start` – Unix timestamp marking when the period begins.
- `campaign_days` / `period_days` – lengths (in days) of the campaign phase and the total governance period. Communities can adjust these values to create short voting windows or extended deliberation periods.
- `composition` and `size` – store the list of elected members and the size of the council.
- `status` – enumerated as `CAMPAIGN`, `TALLY` or `ACTIVE` to indicate whether a council is accepting nominations, tallying votes or serving.

These fields expose the "flexible timelines" and governance phase. By modifying `campaign_days` and `period_days` the community can run daily, weekly or monthly voting rounds.

### Preference pools and opinions

For preferential or quadratic voting, the `PreferencePool` account stores a map of opinions for each voter ID. Each `Opinion` records bonuses and maluses so that votes can include positive and negative weightings. The pool is keyed by the election period and can be reset each cycle. This design provides transparent tracking of proposals through discussion, voting and execution.

## Trust networks & identity

### Identity accounts

The system prevents Sybil attacks by giving each participant a unique identity and requiring them to gain trust from others before voting. Key structures include:

- `TrustList` – holds a vector of assigned identity numbers and the next available ID.
- `Trust` – stores parameters governing trust requirements (`req`) and counts of current trustees (`trustees`).
- `Trustable` – represents an individual user, storing their ID, username, full name, birthday timestamp and lists of IDs they trust or are trusted by. Flags indicate whether they are currently trusted and whether their account is locked.
- `PkLink` – binds an identity to a Solana wallet public key.
- `SimilarIDs` – keeps track of potentially duplicate IDs to help with identity resolution.

### Trust management functions

`lib/shared` includes core algorithms for managing trust:

- `break_trust_fn` – takes two `Trustable` accounts and removes the trust relationship between them. It also decrements the total trustee count and clears the trusted flag if the user no longer meets the cutoff. This function enforces negative trust actions (e.g., revoking endorsements).
- `butcher` – normalizes a name by stripping diacritics and non-ASCII characters, lowercasing and sorting characters. Such normalization can be used when comparing usernames or generating deterministic identity hashes.
- `cutoff` – computes the minimum number of trustees required for someone to be considered trusted (`max(0, req)` clamped to the number of trustees).
- `report_threshold` – calculates how many reports are needed to trigger further action based on the number of trustees.

These functions demonstrate how quorum and support thresholds are enforced in code.

## Getting started

1. **Install prerequisites** – ensure you have Rust, Anchor, the Solana CLI and Node.js installed.
2. **Clone the repository and its submodules** – run:

   ```bash
   git clone --recurse-submodules https://github.com/your-username/ubi-proposal-2025.git
   cd ubi-proposal-2025
   ```

3. **Build the on-chain programs** – run `anchor build` in each program directory (`programs/census`, `programs/trust-networks`, etc.) to compile the smart contracts.
4. **Deploy to a local or testnet cluster** – configure your Solana CLI to point to devnet or localnet and run `anchor deploy` for each program.
5. **Run the frontend** – the `app` directory contains a Next.js application. Install dependencies with `npm install` (or `pnpm install`) and start the development server with `npm run dev`.

## Contributing

We welcome contributions from anyone interested in advancing alternative economic and democratic systems. You can open issues for bugs or feature requests, submit pull requests or improve the documentation.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
