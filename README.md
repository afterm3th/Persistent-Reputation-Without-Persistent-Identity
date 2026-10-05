# Persistent-Reputation-Without-Persistent-Identity
Protocol family: Decentralized Privacy-Preserving Commerce Protocol
# Persistent Reputation Without Persistent Identity

Protocol Specification v0.1

Status: Research / Architecture Draft

Working module name: PRPI

Protocol family: Decentralized Privacy-Preserving Commerce Protocol

---

# 1. Purpose

PRPI defines a cryptographic reputation layer in which a participant can accumulate reputation over time without being required to maintain a permanent publicly observable identity.

The fundamental objective is:

> Preserve continuity of trust without requiring continuity of public identity.

A participant should be able to prove statements such as:

- I have successfully completed more than 100 transactions.
- My dispute rate is below 2%.
- I have participated for more than two years.
- I have never received a serious fraud judgment.
- I possess reputation originating from approved marketplace ecosystems.
- I satisfy a seller's minimum reputation requirement.
- I have completed transactions across multiple independent counterparties.

without revealing:

- their real-world identity;
- their historical usernames;
- their wallet addresses;
- their historical public keys;
- the exact number of transactions;
- individual counterparties;
- transaction amounts;
- their complete reputation history;
- whether two unrelated pseudonyms belong to the same person.

PRPI is intended to be reusable independently of the larger marketplace protocol.

---

# 2. Larger Protocol Context

The surrounding commerce protocol remains loosely defined at this stage.

Its preliminary modules are:

```text
PCP-01   Identity and Pseudonyms
PCP-02   Listings and Offers
PCP-03   Discovery and Indexing
PCP-04   Messaging
PCP-05   Trade Negotiation
PCP-06   Payment and Escrow Interfaces
PCP-07   PRPI Reputation Protocol
PCP-08   Dispute Resolution
PCP-09   Moderation and Policy
PCP-10   Index Federation
PCP-11   Payment Adapters
PCP-12   Application Capability Negotiation
```

PRPI corresponds primarily to PCP-07 but deliberately exposes interfaces that PCP-01, PCP-05, PCP-06 and PCP-08 can consume.

No surrounding module is mandatory for PRPI operation.

PRPI must also be usable by applications unrelated to commerce.

---

# 3. Design Philosophy

PRPI SHALL be designed around the following principles.

## 3.1 Reputation belongs to the participant

Reputation must not depend upon a single marketplace account, server or username.

## 3.2 Identity disclosure is optional

The protocol must not require a legal identity, email address, telephone number or permanent public account identifier.

## 3.3 Pseudonyms are disposable

Public-facing identities should be replaceable without automatically destroying accumulated reputation.

## 3.4 Reputation is difficult to transfer illicitly

Although pseudonyms may change, a reputation credential must not function like a freely transferable NFT.

## 3.5 Proofs reveal predicates, not databases

A verifier should ordinarily learn:

```text
successful_trades >= 100
```

rather than:

```text
successful_trades = 183
```

## 3.6 Negative reputation cannot simply be discarded

The protocol must resist selective migration in which users preserve positive history while abandoning negative history.

Existing UniRep designs are relevant here because UniRep explicitly supports positive and negative reputation and makes reputation non-repudiable once attested.

## 3.7 No mandatory central issuer

Multiple independent systems must be capable of issuing compatible reputation attestations.

## 3.8 No mandatory blockchain

The protocol should define verifiable cryptographic objects independently of any particular settlement network.

A blockchain MAY be used for:

- state commitments;
- revocation roots;
- attester registries;
- timestamping;
- proof verification;
- nullifier tracking.

It MUST NOT be required solely because the protocol is described as decentralized.

---

# 4. Terminology

## Root Secret

A high-entropy secret controlled by a participant.

It provides continuity between private reputation state and disposable public pseudonyms.

It MUST never appear directly on-chain or in public messages.

Conceptually:

```text
root_secret = random(256 bits)
```

## Root Identity Commitment

A one-way commitment derived from the Root Secret.

```text
root_commitment = H(root_secret, domain)
```

The Root Identity Commitment MAY be privately enrolled into reputation systems.

It SHOULD NOT normally serve as a public user identifier.

## Persona

A temporary public identity used within a particular application, marketplace, epoch or context.

Example:

```text
persona_public_key
persona_handle
persona_endpoint
```

## Persona Key

A public/private signing keypair associated with a Persona.

## Epoch

A bounded period during which a Persona or derived identifier remains valid.

Epochs can be based on:

- time;
- transaction count;
- marketplace sessions;
- application-defined state.

UniRep uses temporary epoch keys as anonymous application identities; PRPI adopts the same general concept but makes the lifecycle and portability rules part of the interoperability specification.

## Attester

An entity authorized to issue reputation attestations.

Examples:

- marketplace;
- escrow contract;
- arbitrator;
- buyer;
- seller;
- delivery oracle;
- verification service;
- community moderation system.

## Reputation Attestation

A cryptographically authenticated statement modifying or extending a participant's reputation state.

## Reputation State

The complete private set of reputation attributes associated with a participant.

## Reputation Commitment

A cryptographic commitment to Reputation State.

## Reputation Proof

A zero-knowledge or selectively disclosed proof derived from Reputation State.

## Nullifier

A one-time value derived cryptographically from private identity state that lets a verifier detect prohibited reuse without discovering the underlying identity.

Semaphore demonstrates this pattern for preventing repeated anonymous signaling.

## Reputation Domain

A namespace describing what reputation represents.

Examples:

```text
commerce.general
commerce.digital-goods
commerce.physical-goods
freelance.software
arbitration.general
marketplace.example
```

## Reputation Credential

A portable cryptographic object containing or committing to reputation claims.

---

# 5. Threat Model

PRPI assumes adversaries may attempt to:

1. Create unlimited pseudonyms.
2. Manufacture fake transactions.
3. Rate themselves.
4. Collude with related accounts.
5. Purchase established identities.
6. Transfer reputation to another person.
7. Abandon negative reputation.
8. Link rotated pseudonyms.
9. Correlate transaction timing.
10. Correlate payment addresses.
11. Correlate network metadata.
12. Replay an old proof.
13. Use the same credential simultaneously under many pseudonyms.
14. Forge attestations.
15. Corrupt an attester.
16. Censor a reputation holder.
17. Reveal an individual's complete history.
18. Extract reputation state from a blockchain.
19. Create fraudulent arbitrator judgments.
20. Perform graph analysis across proofs.

The protocol SHALL explicitly document which threats it prevents, mitigates or cannot solve.

---

# 6. Privacy Guarantees

PRPI SHOULD attempt to provide the following guarantees.

## 6.1 Persona unlinkability

Given:

```text
Persona_A
Persona_B
```

an external observer should not be able to determine that both belong to the same Root Identity merely from valid PRPI interactions.

## 6.2 Proof unlinkability

Two ordinary reputation proofs produced by the same holder SHOULD NOT contain a stable identifier that trivially links them.

## 6.3 Minimal disclosure

Proofs SHOULD express predicates.

Example:

```text
trade_count >= 100
```

instead of revealing exact state.

This follows the broader selective-disclosure direction now standardized through W3C Verifiable Credentials 2.0 and associated selective disclosure cryptosuites.

## 6.4 Counterparty privacy

A reputation proof MUST NOT require revealing transaction counterparties.

## 6.5 Transaction privacy

A proof MUST NOT inherently reveal:

- payment address;
- transaction hash;
- asset;
- amount;
- listing;
- delivery address.

## 6.6 Attester privacy

Attester disclosure MAY be configurable.

Some proofs may state:

```text
reputation_from_trusted_attester_set == true
```

without revealing which member issued each historical attestation.

---

# 7. Reputation Model

PRPI SHOULD avoid defining reputation as one universal integer.

Instead, Reputation State consists of typed dimensions.

Example:

```yaml
commerce:
  completed_transactions: 184
  successful_transactions: 179
  disputed_transactions: 5
  seller_cancellations: 2
  buyer_cancellations: 1

value:
  cumulative_volume_bucket: 8
  maximum_trade_bucket: 5

time:
  first_activity_epoch: 19021
  last_activity_epoch: 19843

counterparties:
  unique_counterparty_commitment: ...
  diversity_score: 73

arbitration:
  disputes_won: 3
  disputes_lost: 2
  serious_fault_findings: 0

community:
  endorsements: 21
  complaints: 1
```

Applications SHOULD determine which dimensions matter.

---

# 8. Why Reputation Is Multi-Dimensional

A seller with:

```text
100 transactions × $1
```

should not automatically have equivalent trust to:

```text
100 transactions × $5,000
```

Likewise:

```text
100 trades with one colluding account
```

must not equal:

```text
100 trades with 90 independent counterparties
```

Therefore reputation needs metadata describing quality, age, diversity and economic exposure.

---

# 9. Recommended Reputation Dimensions

The first implementation SHOULD support:

```text
completed_transactions
successful_transactions
failed_transactions
buyer_cancellations
seller_cancellations
disputes_opened
disputes_won
disputes_lost
serious_fault_findings
account_age
transaction_volume_bucket
maximum_trade_bucket
unique_counterparty_score
attester_diversity
recent_activity
```

Implementations MAY define arbitrary extension fields.

---

# 10. Reputation Domains

Every attestation SHALL include a domain.

Example:

```json
{
  "domain": "commerce.physical-goods"
}
```

A verifier MAY accept reputation recursively.

For example:

```text
commerce.general
    ├── physical-goods
    ├── digital-goods
    └── services
```

Applications MUST NOT assume that reputation in one domain automatically transfers to another.

---

# 11. Reputation Attestation

A conceptual attestation:

```json
{
  "version": "prpi/0.1",
  "domain": "commerce.general",
  "subject_commitment": "...",
  "attester": "...",
  "epoch": 21931,
  "event_type": "trade_completed",
  "attributes": {
    "completed_transactions": 1,
    "successful_transactions": 1,
    "volume_bucket": 4
  },
  "context_commitment": "...",
  "nonce": "...",
  "signature": "..."
}
```

`context_commitment` SHOULD allow verification that an attestation corresponds to a genuine protocol event without exposing the complete event.

---

# 12. Positive and Negative Attestations

PRPI SHALL support both.

Examples:

Positive:

```text
transaction completed
buyer paid
seller delivered
dispute resolved in holder's favor
long-term account age
verified unique counterparty
```

Negative:

```text
non-delivery
chargeback
fraud judgment
arbitrator penalty
repeated cancellation
protocol abuse
```

Negative reputation MUST NOT depend on holder acceptance.

Otherwise users could accept positive ratings and reject negative ones.

This is one of the strongest ideas inherited from UniRep, where users cannot selectively refuse reputation from an authorized attester.

---

# 13. Reputation State Commitment

Instead of publishing reputation records directly, the holder maintains private state:

```text
R = {
    completed: 184,
    successful: 179,
    disputes_lost: 2,
    ...
}
```

and publishes or references:

```text
C = Commit(R, secret)
```

Merkle-based or vector commitment structures SHOULD be considered so individual fields can be updated efficiently.

---

# 14. Proof Statements

The protocol SHALL support generic predicate proofs.

Example:

```text
prove:
    completed_transactions >= 100
```

Other examples:

```text
successful_ratio >= 98%
```

```text
serious_fault_findings == 0
```

```text
account_age >= 365 days
```

```text
maximum_trade_bucket >= 6
```

```text
unique_counterparty_score >= 50
```

```text
disputes_lost <= 3
```

Combined:

```text
completed_transactions >= 50
AND
successful_ratio >= 97%
AND
account_age >= 180 days
AND
serious_fault_findings == 0
```

The exact underlying values remain private.

---

# 15. Proof Request

A verifier sends:

```json
{
  "protocol": "prpi/0.1",
  "domain": "commerce.general",
  "requirements": [
    {
      "field": "completed_transactions",
      "operator": ">=",
      "value": 50
    },
    {
      "field": "successful_ratio",
      "operator": ">=",
      "value": 0.97
    }
  ],
  "challenge": "random-verifier-challenge",
  "expires": 1984614012
}
```

---

# 16. Proof Response

The holder returns:

```json
{
  "protocol": "prpi/0.1",
  "proof_type": "zk-reputation-proof",
  "domain": "commerce.general",
  "statement_hash": "...",
  "challenge_hash": "...",
  "epoch": 19843,
  "nullifier": "...",
  "proof": "..."
}
```

No permanent holder identifier is required.

---

# 17. Anti-Replay

Every proof MUST bind to:

```text
verifier challenge
+
purpose
+
expiry
+
domain
```

Example:

```text
proof_context =
H(
  verifier_nonce,
  protocol_action,
  marketplace_domain,
  expiry
)
```

A proof created for one transaction MUST NOT be reusable for another unless explicitly intended.

---

# 18. Nullifier Design

The holder derives a context-specific nullifier:

```text
N = H(
    root_secret,
    external_context,
    epoch,
    action_type
)
```

This allows:

```text
same identity + same action + same context
    → same nullifier
```

while:

```text
same identity + different context
    → unrelated nullifier
```

Uses include:

- preventing multiple ratings for one transaction;
- preventing repeated anonymous votes;
- limiting free account creation;
- enforcing one dispute action per participant;
- controlling reputation migration.

---

# 19. Persistent Reputation

The holder's private Root Identity persists.

Public Personas do not.

Conceptually:

```text
                    ROOT SECRET
                        │
             private reputation state
                        │
        ┌───────────────┼──────────────┐
        ↓               ↓              ↓
     Persona A       Persona B      Persona C
      epoch 10        epoch 11       epoch 12
```

Observers see:

```text
A
B
C
```

but should not learn:

```text
A == B == C
```

The reputation engine nevertheless recognizes that all Personas derive valid proofs from a single evolving private reputation state.

---

# 20. Persona Rotation

Persona rotation SHOULD be a normal protocol operation.

Example:

```text
epoch 100
seller: lunar42

epoch 101
seller: cobalt17

epoch 102
seller: ember92
```

Each Persona may independently prove:

```text
seller reputation > threshold
```

without publicly proving that all three personas belong to the same holder.

---

# 21. Optional Continuity Proof

Sometimes continuity is desirable.

Example:

A seller wants customers to know:

> I am the same seller you bought from last month.

The holder MAY create a pairwise continuity proof:

```text
prove(
    Persona_A and Persona_B
    derive from same root identity
)
```

The proof should be scoped specifically to the intended counterparty.

It MUST NOT create a globally reusable link between personas.

---

# 22. Selective Continuity

Three modes SHOULD exist.

## Anonymous

```text
No continuity revealed.
```

## Pairwise

```text
Continuity revealed only to Counterparty X.
```

## Public

```text
Holder intentionally signs migration:
Persona A → Persona B.
```

Public continuity must always be explicit.

---

# 23. Reputation Migration

Migration does not mean transferring reputation.

Instead it means proving that a new Persona has access to the same private reputation state.

Correct:

```text
root_secret
     │
     ├── Persona A
     └── Persona B
```

Incorrect:

```text
Persona A
     │
 sell credential
     ↓
Persona B belonging to unrelated person
```

---

# 24. Non-Transferability

Perfect prevention of secret sharing is impossible.

If Alice gives Bob her Root Secret, Bob may impersonate Alice cryptographically.

PRPI therefore SHOULD support optional anti-transfer mechanisms.

Examples:

- hardware-backed keys;
- device-bound credentials;
- threshold keys;
- periodic freshness proofs;
- independent uniqueness credentials;
- social recovery groups;
- WebAuthn-backed secrets;
- secure elements.

These SHALL remain optional because mandatory hardware identity would undermine accessibility and anonymity.

---

# 25. Key Hierarchy

Recommended design:

```text
Root Secret
    │
    ├── Recovery Key
    │
    ├── Reputation Key
    │
    └── Persona Derivation Key
              │
              ├── Marketplace A
              │      ├── Epoch 1
              │      └── Epoch 2
              │
              └── Marketplace B
                     ├── Epoch 1
                     └── Epoch 2
```

Compromise of a Persona Key SHOULD NOT expose the Root Secret.

---

# 26. Recovery

Loss of the Root Secret otherwise destroys access to reputation.

PRPI SHOULD therefore define optional recovery.

Possible mechanisms:

## Seed backup

Conventional encrypted recovery phrase.

## Threshold recovery

Example:

```text
2-of-3:

personal backup
trusted device
recovery guardian
```

## Shamir Secret Sharing

Split recovery material among independent locations.

## Hardware backup

Secondary secure hardware key.

Recovery procedures MUST NOT create a publicly observable persistent identity.

---

# 27. Attester Model

Attesters SHALL have independent cryptographic identities.

Example:

```text
AttesterID = public key
```

An attester registry MAY record:

```text
AttesterID
domains
capabilities
stake
trust metadata
revocation state
```

There SHALL NOT be a mandatory global attester authority.

---

# 28. Attester Trust

Different verifiers may trust different attesters.

Example:

```text
Buyer policy:

accept reputation issued by:
    Marketplace A
    Marketplace B
    Arbitrator Network C

ignore:
    Unknown Marketplace D
```

Trust policy belongs to the verifier.

---

# 29. Attester Sets

A proof SHOULD support statements such as:

```text
at least 3 approved attesters contributed
```

without necessarily revealing which three.

This reduces dependence on individual reputation authorities.

---

# 30. Cross-Market Reputation

Portable reputation is one of PRPI's primary differences from application-specific systems.

Example:

```text
Marketplace A
       │
       │ attestations
       ↓
Private Reputation State
       ↑
       │ attestations
Marketplace B
       │
       ↓
Marketplace C proof request
```

Marketplace C can verify:

```text
commerce reputation >= threshold
```

without obtaining the user's identity on Marketplace A or B.

---

# 31. Reputation Import

External systems MAY issue compatible credentials.

Potential sources:

```text
marketplace history
freelance platforms
professional credentials
merchant history
escrow providers
arbitration systems
```

PRPI SHOULD support adapters rather than forcing external systems to adopt its storage model.

---

# 32. W3C Credential Compatibility

PRPI SHOULD evaluate compatibility with W3C Verifiable Credentials 2.0.

Possible architecture:

```text
External VC
     │
     ↓
PRPI credential adapter
     │
     ↓
private reputation claim
```

W3C VC 2.0 explicitly supports selective disclosure and derived presentations, making it suitable for importing conventional credentials without exposing all claims.

---

# 33. Transaction-Bound Reputation

A rating should ordinarily require evidence of a valid transaction.

Conceptually:

```text
Trade Commitment
       │
       ├── Buyer
       ├── Seller
       └── Escrow
              │
         settlement event
              ↓
     Rating entitlement
```

Only participants associated with the trade receive rating rights.

---

# 34. Rating Entitlement

Following settlement:

```text
buyer_rating_token
seller_rating_token
```

are generated or proven.

A rating consumes a nullifier.

Therefore:

```text
one valid trade
→ at most one buyer rating
→ at most one seller rating
```

unless the application explicitly permits additional dimensions.

---

# 35. Sybil Resistance

PRPI cannot prevent the creation of arbitrary pseudonyms.

Instead, it makes newly created pseudonyms lack established reputation.

Possible Sybil resistance layers:

```text
economic stake
transaction cost
proof of unique person
account age
counterparty diversity
attester diversity
rate limits
marketplace admission credentials
```

No one mechanism should be mandatory.

---

# 36. Reputation Farming Resistance

A malicious participant may transact repeatedly with themselves.

Therefore raw transaction count SHOULD NOT be the primary metric.

A reputation calculation may incorporate:

```text
age
counterparty diversity
transaction size
attester diversity
network clustering
dispute history
time distribution
```

Example conceptual weight:

```text
trade_weight =
    age_factor
  × diversity_factor
  × value_factor
  × attester_factor
  × anti_collusion_factor
```

The protocol itself SHOULD transmit primitive reputation facts.

Complex scoring SHOULD generally remain application-defined.

---

# 37. Counterparty Diversity

The system needs to determine that:

```text
100 trades with 100 independent identities
```

is different from:

```text
100 trades with one identity
```

without revealing those identities.

Potential implementation:

Each counterparty produces a privacy-preserving unique commitment.

The reputation holder maintains a set commitment.

A circuit proves:

```text
unique_counterparty_count >= N
```

without revealing the members.

---

# 38. Collusion Detection

Optional analytics layers MAY calculate:

```text
repeated counterparty patterns
circular reputation graphs
timing anomalies
closed trading clusters
abnormal reciprocal ratings
```

These systems SHOULD operate on commitments or selectively disclosed information where possible.

They MUST NOT become mandatory central surveillance services.

---

# 39. Reputation Decay

Applications MAY decrease the relevance of old reputation.

Example:

```text
effective_reputation =
    recent_weight × recent_activity
  + historical_weight × lifetime_activity
```

PRPI SHOULD expose activity epochs so applications can define decay.

The protocol SHALL NOT define a universal decay algorithm.

---

# 40. Reputation Categories

Potential categories:

```text
transactional
delivery
communication
quality
dispute_behavior
payment_behavior
professional
community
arbitration
```

A protocol implementation MUST namespace categories.

---

# 41. Severity

Negative reputation MAY include severity.

Example:

```text
minor
moderate
serious
critical
```

However severity must be attester-authenticated and policy-dependent.

A malicious marketplace must not be able to globally destroy a participant's reputation merely by asserting arbitrary labels that all other applications are forced to accept.

---

# 42. Dispute Reputation

Dispute outcomes SHOULD be cryptographically linked to reputation updates.

Example:

```text
Trade
  │
  ↓
Dispute
  │
  ↓
Arbitration Result
  │
  ├── seller_fault
  ├── buyer_fault
  ├── mutual_resolution
  └── inconclusive
         │
         ↓
Reputation Attestation
```

---

# 43. Negative Reputation and Pseudonym Rotation

This is a core requirement.

Suppose:

```text
Persona A

+120 successful transactions
-7 serious disputes
```

The holder creates Persona B.

Persona B MUST NOT be able to prove:

```text
+120 transactions
```

while hiding the existence of the relevant negative state if the requested predicate depends on it.

For example:

```text
prove successful_ratio >= 98%
```

must evaluate the entire committed state.

---

# 44. Non-Repudiable Updates

The holder cannot simply refuse an unfavorable valid update.

Potential models:

## Global commitment model

Reputation state roots are periodically committed to an append-only state layer.

## Callback model

Attesters can asynchronously update private state through cryptographic callback mechanisms.

Recent zk-promises research is especially relevant here because it demonstrates anonymous private account state where third parties can still apply callbacks for actions such as downvotes or bans while users later prove predicates about updated state.

This should be investigated for PRPI v0.2.

---

# 45. Freshness

A reputation proof SHOULD demonstrate that the holder has incorporated sufficiently recent valid updates.

Otherwise someone could generate a proof immediately before receiving negative reputation and reuse it indefinitely.

Proof:

```text
state_epoch >= current_epoch - allowed_delay
```

Recent zk-promises work uses a conceptually similar requirement where clients periodically scan for callbacks and prove that their private state is sufficiently current.

---

# 46. Revocation

Credentials and attesters may need revocation.

Revocation mechanisms SHOULD support privacy-preserving proofs:

```text
credential is NOT in revocation set
```

without revealing a global permanent credential identifier.

Possible approaches:

- cryptographic accumulators;
- Merkle revocation trees;
- short-lived credentials;
- epoch-based validity.

---

# 47. Reputation Proof Profiles

Applications MAY publish standard profiles.

Example:

```text
PRPI-BASIC-SELLER-1
```

requires:

```text
completed >= 10
serious_faults == 0
state_age <= 7 days
```

Example:

```text
PRPI-HIGH-VALUE-SELLER-1
```

requires:

```text
completed >= 100
success_rate >= 98%
max_trade_bucket >= 7
unique_counterparties >= 50
serious_faults == 0
```

This allows interoperable reputation requirements.

---

# 48. Capability Negotiation

Client:

```json
{
  "prpi_versions": ["0.1"],
  "proof_systems": [
    "groth16",
    "plonk"
  ],
  "credential_formats": [
    "prpi-native",
    "w3c-vc"
  ]
}
```

Verifier selects compatible methods.

The protocol should avoid binding permanently to one ZK proof system.

---

# 49. Proof-System Abstraction

PRPI SHOULD define semantic statements independently from circuits.

Example:

```text
PRPI statement:

completed_transactions >= 100
```

Implementation may use:

```text
Groth16
PLONK
Halo2
STARKs
future systems
```

This is important for long-term cryptographic agility.

---

# 50. Hash Agility

Objects SHALL identify their hash suite.

Example:

```json
{
  "hash": "poseidon2"
}
```

Future migrations must be possible.

---

# 51. Signature Agility

Attestation objects SHALL declare their signature suite.

Example:

```json
{
  "signature_suite": "ed25519"
}
```

or:

```json
{
  "signature_suite": "secp256k1"
}
```

No permanent dependency on one blockchain signature format.

---

# 52. Chain Independence

PRPI MUST distinguish:

```text
reputation protocol
```

from:

```text
state anchoring mechanism
```

Possible anchors:

```text
Ethereum
L2
Bitcoin-derived anchoring
permissioned ledger
distributed log
transparency tree
federated database
pure peer-to-peer storage
```

---

# 53. Transport Independence

PRPI objects MAY travel through:

```text
HTTPS
Tor
I2P
libp2p
Nostr-like relays
direct P2P
QR code
file transfer
Bluetooth
NFC
```

Transport is outside the reputation core.

---

# 54. Marketplace Integration

A future marketplace flow could be:

```text
Buyer finds seller
      │
      ↓
Buyer requests reputation proof
      │
      ↓
Seller generates PRPI proof
      │
      ↓
Buyer verifies proof
      │
      ↓
Trade created
      │
      ↓
Escrow settled
      │
      ↓
Trade produces reputation rights
      │
      ↓
Buyer/Seller issue attestations
      │
      ↓
Private reputation states update
```

---

# 55. Discovery Integration

Search indexers MAY request standardized seller proof profiles.

Example listing:

```text
Seller:
    ephemeral persona: pk_73fa...

Proofs:
    PRPI-BASIC-SELLER-1 ✓
    PRPI-HIGH-VALUE-SELLER-1 ✓
```

The index does not need access to historical usernames.

---

# 56. Messaging Integration

Messaging keys SHOULD be separate from reputation identity.

```text
Root reputation identity
       X
Messaging identity
       X
Payment identity
```

The protocol should minimize unnecessary cross-layer identifiers.

---

# 57. Payment Integration

Payment addresses MUST NOT be used as PRPI identity identifiers.

A marketplace MAY prove:

```text
payment settled
```

through an escrow attester.

PRPI stores the resulting reputation fact rather than requiring users to expose the payment graph.

---

# 58. Escrow Integration

Escrow systems MAY act as trusted or cryptographically constrained attesters.

Example:

```text
escrow contract observes:

funded
released
refunded
timed_out

→ produces attestation
```

This provides stronger reputation evidence than unverified self-reported ratings.

---

# 59. Arbitrator Integration

Arbitrators MAY issue signed outcome attestations.

Example:

```json
{
  "type": "dispute_result",
  "result": "seller_fault",
  "severity": 2,
  "trade_commitment": "...",
  "arbitrator": "..."
}
```

---

# 60. Reputation Markets

PRPI MUST explicitly distinguish between:

```text
reputation
```

and:

```text
stake
```

Stake may be transferable.

Reputation SHOULD represent historical conduct and SHOULD NOT ordinarily be tradable.

---

# 61. Proof-of-Reputation API

Illustrative API:

```text
POST /proof/request
POST /proof/generate
POST /proof/verify
POST /attestation
POST /state/sync
POST /persona/derive
POST /recovery/export
```

These are conceptual interfaces, not mandatory HTTP endpoints.

---

# 62. SDK

The reference project SHOULD eventually provide:

```text
prpi-core
prpi-wallet
prpi-circuits
prpi-verifier
prpi-attester
prpi-sdk-js
prpi-sdk-rust
prpi-sdk-python
prpi-contracts
prpi-cli
```

---

# 63. CLI Concept

Example:

```bash
prpi identity create
```

```bash
prpi persona derive marketplace-a
```

```bash
prpi state show
```

Private local result:

```text
Completed trades:      184
Successful:            179
Disputes lost:           2
Unique counterparties: 137
First activity:       2024-02-11
```

Generate proof:

```bash
prpi prove \
  --completed-min 100 \
  --success-min 0.97 \
  --serious-faults-max 0
```

Verify:

```bash
prpi verify seller-proof.prpi
```

---

# 64. Wallet UX

The user should see understandable statements.

Instead of:

```text
Generate Groth16 proof
```

the client should say:

```text
Marketplace X is asking you to prove:

✓ At least 100 completed transactions
✓ Success rate of at least 97%
✓ No serious unresolved disputes

It will NOT learn:

• your exact transaction count
• previous usernames
• counterparties
• wallet addresses
• transaction amounts
```

---

# 65. Proof Preview

Before generating a proof:

```text
INFORMATION DISCLOSURE

Revealed:
  Seller satisfies Basic Seller Level 3.

Not revealed:
  Exact transaction count
  Exact success rate
  Previous personas
  Transaction history
```

---

# 66. Privacy Budget

Future versions SHOULD consider warning users when combinations of proofs could become identifying.

For example:

```text
trade_count > 937
account_age > 2,941 days
maximum_trade > $28,000
country = Iceland
```

individually reveal little.

Combined, they may identify a specific participant.

The wallet SHOULD detect unusually narrow predicates.

---

# 67. Pairwise Reputation Proofs

A holder MAY generate proofs unique to a verifier.

```text
proof = Generate(
    reputation_state,
    verifier_id,
    nonce
)
```

This makes proofs harder to correlate across independent verifiers.

---

# 68. Reputation Delegation

By default:

```text
reputation delegation = prohibited
```

Future versions MAY define controlled delegation.

Example:

A company proves reputation while employees use independent operational identities.

This is fundamentally different from selling an individual credential.

---

# 69. Organizational Reputation

The architecture SHOULD eventually support:

```text
individual root identity
organization root identity
team membership credential
```

Example:

```text
Alice proves:
    authorized representative of Company X

Company X proves:
    >5 years seller reputation
```

without exposing Alice's private reputation.

---

# 70. Reputation Portability

A core test for compliance:

> Can a user leave Marketplace A and still prove appropriate accumulated reputation on Marketplace B without obtaining Marketplace A's permission?

If the answer is no, the implementation is not fully portable.

---

# 71. Marketplace Exit Resistance

A marketplace disappearing MUST NOT destroy users' historical reputation if they possess valid credentials/state.

Therefore:

```text
marketplace database ≠ sole reputation store
```

Users SHOULD maintain cryptographically verifiable local state.

---

# 72. Backup Format

Export:

```text
.prpi
```

Suggested encrypted container:

```text
PRPI Backup
    version
    encrypted root material
    state commitments
    attestations
    attester metadata
    recovery configuration
```

Encrypted backups MUST use modern authenticated encryption and memory-hard password derivation.

---

# 73. State Synchronization

A holder may use several devices.

Possible design:

```text
Device A
Device B
Device C
    │
encrypted state sync
    │
content-addressed storage
```

The synchronization provider must not learn plaintext reputation state.

---

# 74. Privacy-Preserving State Updates

Future research should investigate mechanisms similar to zk-promises for asynchronous updates against anonymous accounts.

Desired behavior:

```text
Attester issues negative update
         │
         ↓
public callback commitment
         │
         ↓
anonymous holder later synchronizes
         │
         ↓
private state updated
```

The holder cannot indefinitely claim an old clean state.

---

# 75. Proof Freshness Requirement

Verifiers SHOULD specify:

```text
maximum_state_age
```

Example:

```json
{
  "maximum_state_age_seconds": 86400
}
```

This ensures reputation includes updates from approximately the last 24 hours.

---

# 76. Attester Revocation

If an attester's key is compromised:

```text
Attester Registry
      │
      ↓
key revoked at epoch X
```

Proofs SHOULD distinguish attestations:

```text
issued before compromise
issued after compromise
```

Applications determine whether old attestations remain acceptable.

---

# 77. Fork Handling

If reputation state is anchored to distributed consensus, the protocol MUST define fork behavior.

Reputation proofs SHOULD identify:

```text
state_root
epoch
network
```

A verifier determines acceptable finality.

---

# 78. Data Availability

A user must be able to reconstruct state.

Possible sources:

```text
local wallet
distributed storage
attester logs
blockchain events
mirrors
indexers
```

No single archival provider should be mandatory.

---

# 79. Formal Security Properties

The protocol should eventually formally define:

```text
Completeness
Soundness
Zero knowledge
Persona unlinkability
Proof unlinkability
Nullifier uniqueness
Attestation authenticity
State update integrity
Non-repudiation
Replay resistance
Freshness
Domain separation
```

---

# 80. Domain Separation

Every cryptographic derivation MUST include a protocol-specific domain separator.

Example:

```text
PRPI_PERSONA_V1
PRPI_NULLIFIER_V1
PRPI_ATTESTATION_V1
PRPI_PROOF_V1
```

This reduces cross-protocol key confusion.

---

# 81. Metadata Threat Model

Even perfect zero-knowledge proofs do not provide complete anonymity.

Applications integrating PRPI MUST consider:

```text
network address
request timing
payment timing
browser fingerprints
message patterns
shipping information
transaction amount uniqueness
```

PRPI only protects the reputation layer.

---

# 82. Interoperability With Semaphore

Semaphore can potentially provide:

```text
anonymous group membership
anonymous signaling
double-action prevention
```

via identity commitments and nullifiers.

PRPI SHOULD evaluate Semaphore rather than unnecessarily reimplementing equivalent group-membership primitives.

---

# 83. Interoperability With UniRep

UniRep directly overlaps with several PRPI requirements:

```text
anonymous reputation
positive/negative attestations
temporary epoch keys
ZK reputation proofs
private user data
```

UniRep should therefore be treated as:

```text
reference implementation
research dependency candidate
possible underlying primitive
```

rather than as a competitor to ignore.

PRPI's differentiation should focus on:

```text
portable cross-market reputation
standardized commerce reputation vocabulary
persona migration semantics
cross-system attester interoperability
proof profiles
chain independence
credential interoperability
application-independent specification
```

---

# 84. Interoperability With zk-promises

zk-promises adds several capabilities important to PRPI:

```text
anonymous private state
third-party asynchronous updates
banning
reputation-based rate limiting
freshness assertions
```

It therefore deserves direct architectural evaluation before PRPI's private state-update mechanism is finalized.

---

# 85. V0.1 Minimal Viable Protocol

The first implementation SHOULD NOT attempt the entire specification.

MVP:

```text
1. Generate root identity.
2. Derive disposable Personas.
3. Register trusted attesters.
4. Issue positive/negative reputation attestations.
5. Maintain private reputation state.
6. Generate threshold ZK proofs.
7. Verify proofs.
8. Bind proof to challenge.
9. Generate action nullifiers.
10. Rotate Personas.
11. Demonstrate that Personas remain unlinkable.
```

---

# 86. V0.1 Reputation Fields

Only:

```text
completed_transactions
successful_transactions
disputes_lost
serious_fault_findings
first_activity_epoch
unique_counterparty_count
```

This is enough to demonstrate the architecture.

---

# 87. V0.1 Proofs

Required:

```text
completed_transactions >= X
successful_transactions / completed_transactions >= X
disputes_lost <= X
serious_fault_findings == 0
account_age >= X
unique_counterparties >= X
```

---

# 88. V0.1 Demonstration

Create three independent sample marketplaces:

```text
Market A
Market B
Market C
```

User earns reputation on A and B.

They generate a brand-new Persona on C.

Market C asks:

```text
Have you completed >= 50 trades?

Is your success rate >= 95%?

Have you had zero serious fault judgments?
```

User proves:

```text
YES
YES
YES
```

Market C learns no usernames from A or B.

That demonstration captures the entire thesis of the protocol.

---

# 89. Test Scenario

Initial private reputation:

```text
completed = 82
successful = 80
serious_faults = 0
```

Persona:

```text
seller_7ac91
```

Proof:

```text
completed >= 50            TRUE
success_rate >= 95%        TRUE
serious_faults == 0        TRUE
```

Rotate identity:

```text
seller_7ac91
      ↓
deleted/retired

seller_f402d
```

New Persona produces the same valid reputation proof.

Observers must not receive cryptographic evidence that:

```text
seller_7ac91 == seller_f402d
```

---

# 90. Negative Test

State becomes:

```text
completed = 83
successful = 80
serious_faults = 1
```

New Persona attempts:

```text
prove serious_faults == 0
```

Proof generation MUST fail.

Persona rotation MUST NOT reset the negative event.

---

# 91. Replay Test

Verifier sends:

```text
challenge_A
```

Holder returns proof A.

Verifier B sends:

```text
challenge_B
```

Proof A MUST fail under challenge B.

---

# 92. Double-Rating Test

One transaction provides one rating entitlement.

First rating:

```text
nullifier N
accepted
```

Second rating:

```text
nullifier N
rejected
```

Identity remains hidden.

---

# 93. Cross-Market Test

Market A and Market B issue compatible attestations.

The holder proves:

```text
combined completed_transactions >= 100
```

Market C verifies without receiving historical persona identities.

---

# 94. Repository Structure

Recommended initial repository:

```text
/docs
    architecture.md
    threat-model.md
    reputation-model.md
    persona-model.md
    attestations.md
    proofs.md
    nullifiers.md
    portability.md
    privacy.md

/spec
    PRPI-000-core.md
    PRPI-001-identities.md
    PRPI-002-attestations.md
    PRPI-003-reputation-state.md
    PRPI-004-proofs.md
    PRPI-005-nullifiers.md
    PRPI-006-persona-rotation.md
    PRPI-007-portability.md

/research
    unirep.md
    semaphore.md
    zk-promises.md
    w3c-verifiable-credentials.md
    sybil-resistance.md

/reference
    /client
    /verifier
    /attester
    /circuits

/test-vectors
```

---

# 95. Protocol Documents

The eventual protocol should be split into focused documents.

## PRPI-000 Core

Terminology and architecture.

## PRPI-001 Root Identity

Key generation and storage.

## PRPI-002 Persona Derivation

Disposable identities.

## PRPI-003 Attestations

Reputation update format.

## PRPI-004 State

Private reputation state.

## PRPI-005 Proof Language

Predicate specification.

## PRPI-006 Nullifiers

Anti-replay and anti-double-action.

## PRPI-007 Portability

Cross-application reputation.

## PRPI-008 Recovery

Root recovery.

## PRPI-009 Revocation

Credential/attester revocation.

## PRPI-010 Marketplace Profile

Standard commercial reputation fields.

---

# 96. Open Research Questions

The following should remain explicitly unresolved until prototyping.

1. Use UniRep directly or develop a compatible abstraction?
2. Can zk-promises provide the private callback model we need?
3. Which proof system provides acceptable browser/mobile performance?
4. How should counterparty diversity be proven privately?
5. How do we prevent reputation sales sufficiently without compromising anonymity?
6. How should cross-attester reputation be normalized?
7. How should fraudulent attesters be handled?
8. How much reputation metadata can be safely exposed?
9. Should attestation histories remain entirely client-side?
10. Should a neutral public commitment layer exist?
11. Can proofs remain chain-independent while still supporting smart-contract verification?
12. How should recovery work without becoming a persistent identity oracle?
13. How should reputation be inherited by organizations?
14. How should reputation domains interoperate?
15. What privacy leakage results from repeated threshold proofs?

---

# 97. Non-Goals

PRPI does NOT attempt to:

- guarantee network anonymity;
- hide shipping information;
- provide cryptocurrency privacy;
- implement marketplace search;
- operate escrow;
- determine what goods may be sold;
- define legal jurisdiction;
- decide universal reputation scores;
- provide a proof-of-personhood system;
- prevent every form of collusion;
- guarantee that a human never shares their secret keys.

Those functions belong to other protocol layers.

---

# 98. Success Criteria

PRPI succeeds if the following statement becomes technically true:

> A participant can discard every public marketplace identity they have used, create an entirely new pseudonym, and cryptographically prove an established history of trustworthy conduct without revealing their previous identities or complete transaction history.

At the same time:

> That participant cannot escape valid negative reputation merely by creating the new pseudonym.

Those two properties together define the project.

---

# 99. Architectural Summary

```text
                         PRIVATE

                    ROOT IDENTITY
                         │
                         ↓
                REPUTATION STATE
             ┌───────────┼───────────┐
             │           │           │
        positive     negative     historical
       attestations attestations   metadata
             │           │           │
             └───────────┼───────────┘
                         │
                    ZK PROVER
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
    Persona A        Persona B        Persona C
    Market A         Market B         Market C

                         PUBLIC

                 No persistent ID

                 Proofs only:

               reputation > X
               disputes < Y
               history > Z
```

---

# 100. Core Principle

PRPI separates four concepts that conventional marketplaces combine:

```text
IDENTITY
    ≠
REPUTATION
    ≠
ACCOUNT
    ≠
TRANSACTION HISTORY
```

A traditional system says:

```text
trust this account
because this account has history
```

PRPI instead says:

```text
trust this cryptographic proof
because its holder possesses qualifying history
```

The verifier does not need to know which previous identities accumulated that history.

That distinction is the foundation of Persistent Reputation Without Persistent Identity.
