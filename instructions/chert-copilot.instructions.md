---
applyTo: '**'
---

## **CRITICAL: Always Use Serena First (#serena MCP server)**

**For ALL analysis, investigation, and code understanding tasks, use Serena semantic tools:**

### **Standard Serena Workflow**
1. **Start with Serena memories**: Use Serena to list memories and read relevant ones for context #serena
2. **Use semantic analysis**: Use Serena to find [symbols/functions/patterns] related to [issue] #serena
3. **Get symbol-level insights**: Use Serena to analyze [specific function] and show all referencing symbols #serena
4. **Create new memories**: Use Serena to write a memory about [findings] for future reference #serena

### **Serena-First Examples**

# Instead of: "Search the codebase for database queries"
# Use: "Use Serena to find all database query functions and analyze their performance patterns #serena"

# Instead of: "Find all admin functions"  
# Use: "Use Serena to get symbols overview of admin files and find capability-checking functions #serena"

# Instead of: "How do the three systems integrate?"
# Use: "Use Serena to read the system-integration-map memory and show cross-system dependencies #serena

# Chert Coin Project - GitHub Copilot Instructions

## Project Overview
Chert is a next-generation blockchain project implementing Silica v2 - a high-performance consensus system using Narwhal/Bullshark + aBFT with post-quantum cryptography. The project prioritizes security, performance, and scientific integration through Network Utility Work (NUW).

## Rust Development Standards

### Code Quality & Safety (TigerBeetle-Inspired Excellence)
- Use latest stable Rust (edition 2024) with strict safety practices
- **NEVER** use unsafe code without explicit justification and security review
- **ZERO TECHNICAL DEBT POLICY**: Do it right the first time, every time
- **NEVER** mark work as "complete" if it contains ANY of the following:
  - "for now" comments
  - "TODO" markers
  - "FIXME" markers
  - "HACK" comments
  - "TEMP" or "TEMPORARY" comments
  - "in production" qualifiers
  - "simplified" implementations
  - Stubbed functions
  - Placeholder returns
  - Mock/fake data
  - **Work with these markers MUST remain "in progress" until fully implemented**
- Implement comprehensive error handling with `Result<T, E>` and `anyhow`/`thiserror`
- Use strong typing and avoid primitive obsession
- Handle all edge cases and potential panics gracefully
- Validate all external inputs at system boundaries
- **Assert everything**: Minimum 2 assertions per function (NASA Power of Ten Rules)
- **Fail fast**: Assertions downgrade catastrophic correctness bugs into liveness bugs
- **Static bounds**: All loops, queues, and collections must have fixed upper bounds

### Required Tools & Linting
```bash
# Always run before committing (MANDATORY)
cargo fmt --all           # Code formatting
cargo clippy --all-targets --all-features -- -D warnings  # Linting
cargo audit               # Security vulnerability scan
cargo test --workspace    # Full test suite
cargo bench               # Performance regression detection
cargo doc --open          # Documentation generation and validation

# Quality gates (must pass)
cargo clippy -- -D clippy::unwrap_used -D clippy::expect_used  # No panics in production
cargo test --release      # Test optimized builds
```

## Security Requirements (CRITICAL)

### Cryptographic Standards
**✅ APPROVED Libraries:**
```rust
// Post-quantum signatures (PRIMARY)
use pqcrypto_dilithium::dilithium2;  // Consensus operations
use pqcrypto_kyber::kyber768;        // Key exchange
use ed25519_dalek;                   // Network identity (legacy compatibility)

// Symmetric crypto
use ring::aead::AES_256_GCM;         // Authenticated encryption  
use argon2::Argon2;                  // Password hashing
use blake3;                          // Fast hashing
use sha3::Keccak256;                 // Consensus hashing
use rand::rngs::OsRng;               // Cryptographically secure RNG
```

**❌ FORBIDDEN:**
- No custom cryptographic implementations
- No deprecated algorithms (SHA-1, MD5, DES)
- No weak random number generators (`thread_rng()`)
- No hardcoded secrets or credentials

### Input Validation
- Validate ALL external inputs (network, files, APIs, user input)
- Use bounded data structures and check for DoS vectors
- Sanitize inputs before processing or storage
- Implement proper authentication and authorization checks

### Memory Safety & Performance (TigerBeetle Standards)
- **Static Memory Allocation**: ALL memory must be allocated at startup - NO dynamic allocation after initialization
- **Cache Alignment**: All hot data structures MUST be 64-byte aligned (`#[repr(align(64))]`)
- **Block Size Standard**: Use 64KB blocks for all storage operations (optimal for SSDs and memory pages)
- **Zero-Copy Design**: Eliminate memory copies wherever possible - use references and memory mapping
- **Deterministic Performance**: All operations must have bounded execution time and memory usage
- Zeroize sensitive data after use (`zeroize` crate)
- Use secure memory allocation for cryptographic operations
- Implement proper resource cleanup in all code paths
- Monitor for memory leaks and unsafe patterns

### Performance Standards (Mechanical Sympathy)
- **Batching First**: Amortize all costs (network, disk, memory, CPU) through batching
- **Single-Threaded Hot Paths**: Prefer single-threaded execution with large batches over multi-threading
- **Branchless Algorithms**: Optimize for CPU pipeline efficiency - lift branching out of hot loops
- **Static Pools**: Pre-allocate object pools for all frequently used types
- **Direct I/O**: Use io_uring and Direct I/O patterns for storage operations
- **Avoid Allocations**: Zero allocations in hot paths after initialization
- **Fixed Upper Bounds**: Every loop, queue, buffer must have compile-time or startup-time bounds

## Architecture-Specific Guidelines

### Consensus System (Narwhal/Bullshark)
- Follow Byzantine fault tolerance assumptions
- Implement proper committee selection with VRF
- Handle network partitions and malicious actors
- Use deterministic state machines for finality

### Execution Engine (Chipmunk)
- Implement proper gas metering for WASM contracts
- Use sandboxed execution environments
- Handle cross-shard communication securely
- Implement proper data availability checks

### Network Layer (QUIC/libp2p)
- Use TLS encryption for all communications
- Implement proper peer authentication
- Handle connection limits and rate limiting
- Validate network messages before processing

### Scientific Integration (NUW)
- Verify scientific computation results cryptographically
- Implement proper work validation with fraud proofs
- Handle third-party API integrations (BOINC, Folding@Home) securely
- Protect against gaming, Sybil attacks, and result manipulation
- Use deterministic verification for reproducible results
- Implement timeout and resource limits for scientific workloads

## Project Structure

### Workspace Organization
```
chert/
├── silica/           # Core blockchain node (main binary)
├── silica-models/     # Shared data structures and types
├── miner/           # Mining and proof-of-work components
├── poi/             # Proof of Intelligence implementation
├── wallet/          # Wallet implementation (includes Tauri GUI)
├── cli/             # Command-line tools and utilities
├── contracts/       # Smart contracts and WASM runtime
├── api/             # API server and RPC interfaces
└── infrastructure/  # Deployment and infrastructure code
```

### Dependency Management
- Use `workspace = true` for shared dependencies in Cargo.toml
- Internal crates: `silica-models = { path = "../silica-models" }`
- Always specify exact versions for cryptographic dependencies
- Use feature flags to conditionally compile expensive components

### Common Patterns (TigerBeetle-Inspired)
- Use workspace dependencies for consistency (`workspace = true`)
- Implement proper feature flags for optional functionality
- Follow async/await patterns with tokio runtime
- Use structured logging with `tracing` crate (not `log`)
- Prefer `postcard` for internal serialization, `serde_json` for external APIs
- Use `anyhow` for application errors, `thiserror` for library errors

### Mandatory Coding Patterns
```rust
// 1. Cache-aligned hot data structures
#[repr(align(64))]
pub struct HotDataStructure {
    // Critical fields first, cache-line aligned
    critical_counter: AtomicU64,
    hot_buffer: [u8; 64],
}

// 2. Static memory pools (allocated at startup)
pub struct ComponentManager {
    static_pool: StaticPool<ComponentData, 10000>, // Fixed size at compile time
    ring_buffer: RingBuffer<Message, 65536>,       // Power of 2 sizes
}

// 3. Bounded operations with assertions
pub fn process_batch(items: &[Item]) -> Result<Vec<Result>, Error> {
    assert!(items.len() <= MAX_BATCH_SIZE, "Batch size exceeds static limit");
    assert!(!items.is_empty(), "Empty batch not allowed");
    
    let mut results = Vec::with_capacity(items.len()); // Pre-allocate
    
    // Process with bounded loop
    for (i, item) in items.iter().enumerate() {
        assert!(i < MAX_BATCH_SIZE, "Loop bound exceeded");
        let result = process_item_deterministic(item)?; // No branching in hot path
        results.push(result);
    }
    
    assert_eq!(results.len(), items.len(), "Result count mismatch");
    Ok(results)
}

// 4. Deterministic operations with assertions
pub fn deterministic_hash(data: &[u8]) -> [u8; 32] {
    assert!(!data.is_empty(), "Empty data not allowed");
    assert!(data.len() <= MAX_DATA_SIZE, "Data exceeds maximum size");
    
    let mut hasher = blake3::Hasher::new();
    hasher.update(data);
    let result = hasher.finalize();
    
    assert_ne!(result.as_bytes(), &[0; 32], "Zero hash indicates error");
    *result.as_bytes()
}

// 5. Resource management with static bounds
pub struct ResourceManager {
    memory_pool: [MemoryBlock; STATIC_POOL_SIZE],
    active_connections: BoundedVec<Connection, MAX_CONNECTIONS>,
    message_queue: BoundedQueue<Message, QUEUE_CAPACITY>,
}
```

## Testing Requirements (Comprehensive & Deterministic)

### Test Coverage (Zero Defect Policy)
- **Unit tests for ALL public functions** - no exceptions
- **Integration tests for component interactions** - test real-world scenarios
- **Fuzzing tests for parsing and validation logic** - discover edge cases
- **Security tests for cryptographic operations** - verify against known attack vectors
- **Performance benchmarks for critical paths** - detect regressions immediately
- **Deterministic tests**: All tests must produce identical results across runs
- **Memory leak tests**: Verify no dynamic allocations in production paths
- **Fault injection tests**: Test error handling and recovery mechanisms

### Test Structure (TigerBeetle Quality Standards)
```rust
#[cfg(test)]
mod tests {
    use super::*;
    
    #[tokio::test]
    async fn test_functionality() {
        // Arrange - Set up deterministic test environment
        // Act - Execute with bounded inputs
        // Assert - Verify ALL postconditions and invariants
        
        // MANDATORY: Test both positive and negative cases
        // MANDATORY: Assert resource bounds are respected
        // MANDATORY: Verify no dynamic allocations occurred
    }
    
    #[test]
    fn test_security_edge_case() {
        // Test malicious inputs at boundaries
        // Verify proper error handling without panics
        // Assert system remains in valid state
        // Test DoS resistance and resource limits
    }
    
    #[test]
    fn test_performance_bounds() {
        // Verify operations complete within time bounds
        // Assert memory usage stays within static limits
        // Test batch processing efficiency
        // Measure and assert cache hit rates
    }
    
    #[test]
    fn test_deterministic_behavior() {
        // Run same operation multiple times
        // Assert identical results every time
        // Verify state consistency across runs
        // Test with fixed seeds for reproducibility
    }
}
```

## Documentation Standards
- Document all public APIs with rustdoc
- Include security considerations in function docs
- Provide usage examples for complex functionality
- Update SECURITY_GUIDELINES.md for security-related changes

## Performance Considerations
- Benchmark critical paths (consensus, networking, crypto)
- Use efficient data structures (avoid unnecessary allocations)
- Implement proper caching strategies
- Monitor resource usage and set limits

## Error Handling Patterns (Robust & Secure)
```rust
// Use anyhow for application errors
use anyhow::{Result, Context};

// Use thiserror for library errors
use thiserror::Error;

#[derive(Error, Debug)]
pub enum ChertError {
    #[error("Invalid transaction: {0}")]
    InvalidTransaction(String),
    #[error("Network error")]
    Network(#[from] std::io::Error),
    #[error("Resource limit exceeded: {resource} > {limit}")]
    ResourceLimitExceeded { resource: String, limit: usize },
    #[error("Static bound violation: {operation}")]
    StaticBoundViolation { operation: String },
}

// Defensive validation with bounds checking
fn validate_input_defensive(data: &[u8]) -> Result<ValidatedData> {
    // Multiple assertion layers for safety
    assert!(!data.is_empty(), "Input data cannot be empty");
    assert!(data.len() <= MAX_INPUT_SIZE, "Input exceeds static maximum");
    
    if data.len() > MAX_SAFE_SIZE {
        return Err(ChertError::ResourceLimitExceeded {
            resource: "input_size".to_string(),
            limit: MAX_SAFE_SIZE,
        });
    }
    
    // Validate all bytes are within expected range
    for (i, &byte) in data.iter().enumerate() {
        if i >= MAX_INPUT_SIZE {
            return Err(ChertError::StaticBoundViolation {
                operation: "input_validation_loop".to_string(),
            });
        }
        // Additional byte-level validation
    }
    
    // Never expose sensitive information in errors
    ValidatedData::from_bytes(data)
        .with_context(|| "Data validation failed")
}

// Function-level resource bounds enforcement
pub fn bounded_operation<T>(items: &[T]) -> Result<ProcessedData> 
where
    T: Clone + Send + Sync,
{
    // Static bound enforcement
    const MAX_ITEMS: usize = 10_000;
    
    assert!(items.len() <= MAX_ITEMS, "Items exceed compile-time maximum");
    
    if items.is_empty() {
        return Err(ChertError::InvalidTransaction(
            "Empty item list not allowed".to_string()
        ));
    }
    
    // Pre-allocate with known bounds
    let mut results = Vec::with_capacity(items.len());
    
    // Bounded processing loop
    for (index, item) in items.iter().enumerate() {
        // Prevent infinite or unbounded loops
        assert!(index < MAX_ITEMS, "Loop index exceeded static bound");
        
        let result = process_single_item(item)
            .with_context(|| format!("Failed to process item at index {}", index))?;
        
        results.push(result);
    }
    
    assert_eq!(results.len(), items.len(), "Result count mismatch detected");
    
    Ok(ProcessedData::new(results))
}
```

## Compliance Requirements
- Follow all guidelines in SECURITY_GUIDELINES.md
- Ensure code passes security audit checklist
- Implement proper logging without exposing secrets
- Handle regulatory compliance for blockchain operations

## Development Workflow
1. Create feature branch from main
2. Run security checks and tests locally
3. Ensure all linting passes
4. Submit PR with security review checklist
5. Address all security concerns before merge

## Completion Criteria (MANDATORY)

### Definition of "Done" - Code CANNOT be marked complete if:
- ❌ Contains "for now" comments
- ❌ Contains "TODO" markers (any severity)
- ❌ Contains "FIXME", "HACK", "TEMP", "XXX" markers
- ❌ Contains "in production" qualifiers suggesting current code is temporary
- ❌ Contains "simplified" with promise of future enhancement
- ❌ Has placeholder implementations (returning mock/default/zero values)
- ❌ Has stubbed functions (logging instead of acting)
- ❌ Has commented-out code that should be implemented
- ❌ Has disabled tests that should pass
- ❌ Missing required tests
- ❌ Missing required documentation
- ❌ Fails security audit checklist

### Code CAN be marked complete only when:
- ✅ All functionality fully implemented (no stubs)
- ✅ All edge cases handled
- ✅ All tests passing (including integration tests)
- ✅ Full test coverage for new code (90%+)
- ✅ All documentation complete
- ✅ Security review passed
- ✅ Performance benchmarks met
- ✅ Code review approved
- ✅ Zero TODO/FIXME/HACK comments in completed sections
- ✅ Ready for production use without modification

### When Reviewing PRs - Reject if:
- PR claims to "complete" work but contains TODOs
- PR adds more TODOs than it removes
- PR marks tasks as "done" but has incomplete implementations
- PR has "for now" solutions in supposedly completed code

### Status Marking Rules:
- **"not-started"**: Work not yet begun
- **"in-progress"**: Actively working OR contains any TODOs/stubs
- **"blocked"**: Cannot proceed due to external dependency
- **"complete"**: Fully implemented, tested, documented, production-ready

**CRITICAL**: If you find yourself adding a "TODO" or "for now" comment, the work is NOT complete. Mark it as "in-progress" and create a tracking issue.

Remember: Security is the top priority. When in doubt about security implications, consult the security team and reference SECURITY_GUIDELINES.md.