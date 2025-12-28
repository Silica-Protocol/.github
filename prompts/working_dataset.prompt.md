---
mode: agent
---
Define the task to achieve, including specific requirements, constraints, and success criteria.

## Ledger column families (RocksDB)
- `blocks`: serialized `Block` records keyed by `block:{number}`
- `transactions`: `TransactionRecord` structures keyed by `tx:{id}`
- `state`: raw state values keyed by composite `StateKey`
- `state_roots`: historical state root snapshots
- `snapshots`: archived state images for recovery
- `metadata`: chain-wide scalars (latest block, consensus height, state root)
- `accounts`: serialized `Account` structures keyed by `account:{address}`
- `indices`: secondary lookup tables (block hash → number, sender → tx)
- `supply`: total supply checkpoints stored under `supply_total`
- `code`: contract bytecode blobs
- `contract_storage`: per-contract persistent storage cells
- `finality_archive`: compressed finality segments written by `FinalityArchiveStore`
- `finality_index`: index metadata for archive segment retrieval
- `finality_checkpoints`: finalized checkpoint manifests

## Compression manager quick facts
- Uses `zstd::bulk::{compress,decompress}` with bounds checks and level guard rails (1-21)
- Enforces static buffer caps: 8 MiB payload ceiling, 96-byte envelope overhead
- Wraps data with checksum validation via `blake3`; rejects mismatched hashes
- Disabled mode short-circuits to plaintext copy while preserving asserts
- Unit tests cover invalid level selection, oversize payload rejection, and checksum failures

## Finality archive envelope
- Each segment stored as: `[u64 len_le][32-byte blake3 checksum][payload bytes]`
- `len` must match payload length and remain ≤ configured compression cap
- Checksum computed on the uncompressed payload; mismatch triggers `ArchiveError::CorruptedSegment`
- Compression applied once when writing; reads decompress via shared `CompressionManager`

## Active remediation focus (storage subsystem)
- Replace legacy `storage::schema` references inside `storage/manager.rs`
- Wire DirectIO, batch writer, and metrics collectors into storage coordinator init path
- Expand validation suite to cover ledger <-> archive integration (state root, checkpoint replay)

