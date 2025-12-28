# Silica v2 - AI Developer Starter Guide

**Updated:** September 28, 2025  
**Status:** Production-Ready Development Standards  
**Performance Target:** 1,000,000+ TPS with deterministic execution  

---

## 🎯 **Project Overview**

**Silica v2** is a next-generation blockchain implementing high-performance consensus with **Narwhal/Bullshark + aBFT** and post-quantum cryptography. The project combines TigerBeetle-inspired mechanical sympathy, CockroachDB-style distributed coordination, and Sui-style async parallelism to achieve unprecedented performance.

**Key Innovation**: Hybrid sharding architecture delivering 100,000+ TPS per shard with deterministic execution and automatic load balancing.

---

## 🏗️ **Core Architecture**

### **Consensus System**
- **Narwhal Data Plane**: High-throughput transaction ingestion with micro-batch gossip
- **Bullshark Control Plane**: Committee-based ordering with timeout handling  
- **aBFT Finality**: Mathematical irreversibility with 2/3 supermajority
- **VRF Committee Selection**: Cryptographically secure validator rotation with reputation weighting

### **Execution Engine (Chipmunk)**
- **Phase 1**: Single-shard parallel execution with lane-based conflict detection (✅ Complete)
- **Phase 2A**: Multi-shard coordination with TigerBeetle batching (🚀 In Progress)
- **Cross-Shard Receipts**: Sui-style async coordination with Merkle proof verification
- **WASM Runtime**: Sandboxed smart contract execution with gas metering

### **Sharding Architecture**
- **TigerBeetle-Inspired Batching**: 8K+ transactions per batch for maximum throughput
- **CockroachDB-Style Range Management**: Automatic shard splitting and merging
- **Deterministic State**: Hash-chaining for byte-identical state across replicas
- **Adaptive Load Balancing**: Dynamic shard rebalancing based on real-time metrics

---

## 🐅 **CRITICAL: TigerBeetle-Inspired Quality Standards**

### **Zero Technical Debt Policy**
- **Do it right the first time, every time** - no compromises on architecture
- **Static allocation only** - no dynamic memory allocation after startup
- **Assert everything** - minimum 2 assertions per function (NASA Power of Ten Rules)
- **Bounded operations** - all loops, queues, collections have fixed upper bounds
- **Deterministic execution** - byte-identical results across runs and replicas

### **Memory Management Standards**
```rust
// 1. Cache-aligned hot data structures (64-byte boundaries)
#[repr(align(64))]  
pub struct HotDataStructure {
    critical_counter: AtomicU64,
    hot_buffer: [u8; 64],
}

// 2. Static memory pools (allocated at startup)
pub struct ComponentManager {
    static_pool: StaticPool<ComponentData, 10000>, // Fixed size at compile time
    ring_buffer: RingBuffer<Message, 8192>,        // Power of 2 sizes
}

// 3. Zero-copy message passing
pub struct ZeroCopyChannel {
    shared_memory: MemoryMappedRegion,
    producer_index: AtomicUsize,
    consumer_index: AtomicUsize,
}
```

### **Performance Standards**
- **Batch everything**: 8K+ items per operation (TigerBeetle standard)
- **Single-threaded hot paths**: Avoid context switching in critical sections
- **Branchless algorithms**: Lift branching out of inner loops for CPU pipeline efficiency
- **Direct I/O**: Use io_uring, bypass kernel buffer cache for storage operations
- **Mechanical sympathy**: Work with hardware, not against it

---

## 📁 **Project Structure**

```
silica-network/chert/
├── silica/                 # Core blockchain node (main binary)
│   ├── src/
│   │   ├── consensus/      # Narwhal/Bullshark + aBFT
│   │   ├── execution/      # Chipmunk execution engine + sharding
│   │   ├── storage/        # MVCC storage with Direct I/O
│   │   ├── networking/     # QUIC networking with receipt batching
│   │   ├── vrf/            # Committee selection with reputation
│   │   └── recovery/       # Byzantine fault recovery
├── silica-models/           # Shared data structures and types
├── miner/                  # Mining and proof-of-work components
├── poi/                    # Proof of Intelligence (NUW) implementation
├── wallet/                 # Wallet with Tauri GUI
├── cli/                    # Command-line tools and utilities
├── contracts/              # Smart contracts and WASM runtime
├── api/                    # REST/gRPC API services
├── docs/technical/         # Technical specifications
│   ├── sharding-architecture.md    # Complete sharding design
│   ├── tigerbeetle-optimizations.md # Performance optimizations
│   └── chipmunk.md         # Execution engine specification
└── infrastructure/         # Deployment and infrastructure
```

---

## 🛠️ **Technology Stack**

### **Core Technologies**
- **Language**: Rust (edition 2024) with strict safety practices
- **Consensus**: Custom Narwhal/Bullshark + aBFT implementation
- **Database**: RocksDB with Direct I/O optimization
- **Networking**: QUIC with libp2p, io_uring integration  
- **Smart Contracts**: WebAssembly (Wasmtime runtime)
- **Cryptography**: Post-quantum (Dilithium-2, Kyber-768) + Ed25519

### **Performance Stack**
- **Memory**: Static allocation with cache-aligned structures
- **Storage**: 64KB blocks with deterministic checksums (TigerBeetle pattern)
- **Network**: Control/data plane separation with compression
- **CPU**: SIMD optimization for cryptographic operations

---

## 🎯 **Mandatory Coding Patterns**

### **Every New Component Must Follow This Pattern**
```rust
#[repr(align(64))]  // Cache-aligned for performance
pub struct MyComponent {
    // Static pools for all object types
    transaction_pool: StaticPool<Transaction, 100000>,
    message_pool: RingBuffer<Message, 8192>,
    
    // Bounded collections with compile-time limits
    active_connections: BoundedVec<Connection, 1000>,
    pending_operations: BoundedQueue<Operation, 4096>,
}

impl MyComponent {
    /// Process batch with TigerBeetle-style efficiency
    pub fn process_batch(items: &[Item]) -> Result<Vec<ProcessedItem>, Error> {
        // Defensive assertions (mandatory)
        assert!(items.len() <= MAX_BATCH_SIZE, "Batch exceeds static limit");
        assert!(!items.is_empty(), "Empty batch not allowed");
        
        // Pre-allocate with known bounds
        let mut results = Vec::with_capacity(items.len());
        
        // Bounded processing loop (no unbounded iteration)
        for (i, item) in items.iter().enumerate() {
            assert!(i < MAX_BATCH_SIZE, "Loop bound exceeded");
            
            // Deterministic processing (no branching in hot path)
            let result = process_item_deterministic(item)?;
            results.push(result);
        }
        
        // Verify postconditions (mandatory)
        assert_eq!(results.len(), items.len(), "Result count mismatch");
        Ok(results)
    }
    
    /// Deterministic operation with extensive validation
    fn process_item_deterministic(item: &Item) -> Result<ProcessedItem, Error> {
        // Input validation with bounds checking
        assert!(item.size() <= MAX_ITEM_SIZE, "Item exceeds maximum size");
        assert!(item.is_valid(), "Item failed validation");
        
        // Process with deterministic algorithms
        let processed = ProcessedItem::from_item_deterministic(item)?;
        
        // Output validation
        assert!(processed.checksum_valid(), "Processed item checksum invalid");
        Ok(processed)
    }
}
```

---

## 🔧 **Development Workflow**

### **Required Tools & Quality Gates**
```bash
# MANDATORY: Run before every commit
cargo fmt --all                           # Code formatting
cargo clippy --all-targets --all-features -- -D warnings  # Strict linting
cargo audit                               # Security vulnerability scan
cargo test --workspace                    # Full test suite
cargo bench                               # Performance regression detection
cargo doc --open                          # Documentation validation

# Quality gates (must pass)
cargo clippy -- -D clippy::unwrap_used -D clippy::expect_used  # No panics
cargo test --release                      # Test optimized builds
```

### **Testing Standards (Zero Defect Policy)**
```rust
#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_deterministic_processing() {
        // Arrange - Set up deterministic test environment
        let input = create_test_input_deterministic();
        
        // Act - Execute multiple times
        let results: Vec<_> = (0..10)
            .map(|_| process_batch(&input))
            .collect();
        
        // Assert - Verify identical results every time
        for result in &results[1..] {
            assert_eq!(&results[0], result, "Non-deterministic behavior detected");
        }
        
        // Assert resource bounds respected
        assert!(get_memory_allocations() == 0, "Dynamic allocation detected");
        assert!(get_processing_time() < MAX_PROCESSING_TIME, "Time bound exceeded");
    }
    
    #[test]
    fn test_security_boundaries() {
        // Test malicious inputs at boundaries
        let malicious_input = create_malicious_input();
        
        // Verify proper error handling without panics
        let result = process_batch(&malicious_input);
        assert!(result.is_err(), "Should reject malicious input");
        
        // Assert system remains in valid state
        assert!(system_state_valid(), "System corrupted by malicious input");
    }
    
    #[test]
    fn test_performance_bounds() {
        let large_batch = create_max_size_batch();
        
        let start = std::time::Instant::now();
        let result = process_batch(&large_batch).unwrap();
        let duration = start.elapsed();
        
        // Verify operations complete within time bounds
        assert!(duration < MAX_BATCH_PROCESSING_TIME, "Performance regression");
        assert_eq!(result.len(), large_batch.len(), "Incomplete processing");
    }
}
```

---

## 🚀 **Current Implementation Status**

### **✅ Completed (Production Ready)**
- **Consensus Framework**: Narwhal/Bullshark + aBFT with VRF committee selection
- **Single-Shard Execution**: Chipmunk Phase 1 with lane-based parallelism
- **Post-Quantum Cryptography**: Dilithium-2 + Kyber-768 integration
- **Privacy Layer**: Stealth addresses with double ratchet encryption
- **Staking System**: Auto-staking, LockBox, fee burn mechanisms
- **Governance Framework**: Parameter proposals with emergency response

### **🚀 In Active Development (Phase 2A - Highest Priority)**
- **Sharding Foundation**: TigerBeetle batching + CockroachDB ranges + Sui receipts
- **Static Memory Allocation**: Eliminating dynamic allocation across all components
- **Cache-Aligned Structures**: 64-byte alignment for all hot data paths
- **Direct I/O Integration**: io_uring for maximum storage performance

### **📋 Planned (Phase 2B-2C)**
- **Cross-Shard Coordination**: AsyncReceiptSystem with Merkle proof verification
- **Performance Optimization**: SIMD, mechanical sympathy, 1M+ TPS target
- **Network Utility Work**: Scientific computing validation (BOINC/Folding@Home)
- **Production Deployment**: CI/CD pipelines and infrastructure automation

---

## 📊 **Performance Targets**

| **Component** | **Current** | **Target** | **Strategy** |
|---------------|-------------|------------|--------------|
| **Consensus TPS** | 10,000+ | 50,000+ | Narwhal batching optimization |
| **Single Shard TPS** | 25,000+ | 100,000+ | TigerBeetle-style execution |
| **Global TPS** | Phase 1 | 1,000,000+ | Multi-shard coordination |
| **Finality** | 3 seconds | 1 second | aBFT optimization |
| **Memory Efficiency** | 80% | 95%+ | Static allocation + caching |

---

## 🔐 **Security Requirements (Critical)**

### **Cryptographic Standards**
```rust
// ✅ APPROVED: Post-quantum ready
use pqcrypto_dilithium::dilithium2;  // Consensus signatures
use pqcrypto_kyber::kyber768;        // Key exchange
use ed25519_dalek;                   // Network identity
use ring::aead::AES_256_GCM;         // Authenticated encryption
use blake3;                          // Fast hashing
use rand::rngs::OsRng;               // Cryptographically secure RNG

// ❌ FORBIDDEN: Weak or deprecated
// - No custom crypto implementations
// - No SHA-1, MD5, DES
// - No thread_rng() or weak randomness
// - No hardcoded secrets
```

### **Input Validation Patterns**
```rust
fn validate_external_input(data: &[u8]) -> Result<ValidatedData, SecurityError> {
    // Multiple assertion layers for defense in depth
    assert!(!data.is_empty(), "Empty input not allowed");
    assert!(data.len() <= MAX_INPUT_SIZE, "Input exceeds static maximum");
    
    // Validate at system boundary
    if data.len() > MAX_SAFE_SIZE {
        return Err(SecurityError::InputTooLarge { 
            size: data.len(), 
            max: MAX_SAFE_SIZE 
        });
    }
    
    // Byte-level validation with bounds checking
    for (i, &byte) in data.iter().enumerate() {
        if i >= MAX_INPUT_SIZE {
            return Err(SecurityError::BoundViolation("input_validation_loop"));
        }
        if !is_valid_byte(byte) {
            return Err(SecurityError::InvalidByte { index: i, value: byte });
        }
    }
    
    // Never expose sensitive data in errors
    ValidatedData::from_bytes_secure(data)
        .map_err(|_| SecurityError::ValidationFailed)
}
```

---

## 🎯 **AI Development Guidelines**

### **For AI Assistants Working on Silica v2**

1. **Always Apply TigerBeetle Standards**: Every new component must use static allocation, cache alignment, and bounded operations
2. **Security First**: Validate all inputs, use approved cryptographic libraries, never expose secrets
3. **Performance Mindset**: Think in terms of batching, mechanical sympathy, and deterministic execution
4. **Zero Technical Debt**: Implement complete, production-ready solutions from the start
5. **Comprehensive Testing**: Include unit, integration, security, and performance tests
6. **Documentation**: Every public API must have security considerations and usage examples

### **Quality Checklist for Every Code Change**
- [ ] Uses static memory allocation patterns
- [ ] Hot data structures are cache-aligned (`#[repr(align(64))]`)
- [ ] All operations have bounded resource usage
- [ ] Minimum 2 assertions per function
- [ ] Comprehensive error handling with proper types
- [ ] Deterministic behavior with reproducible results
- [ ] Security validation at system boundaries
- [ ] Performance benchmarks for critical paths
- [ ] Complete test coverage (unit + integration + security)

---

## 📚 **Essential Technical Documentation**

### **Architecture Specifications**
- **`docs/technical/sharding-architecture.md`** - Complete sharding design with performance targets
- **`docs/technical/tigerbeetle-optimizations.md`** - System-wide optimization strategies  
- **`docs/technical/chipmunk.md`** - Execution engine specification
- **`silica/design_plan.md`** - Implementation roadmap and priorities

### **Implementation References**  
- **`silica/src/execution.rs`** - Single-shard parallel execution engine
- **`silica/src/storage/mvcc.rs`** - MVCC storage with RocksDB backend
- **`silica/src/consensus/`** - Narwhal/Bullshark consensus implementation
- **`silica/src/vrf/`** - Committee selection with reputation system

---

## 🚀 **Ready to Build the Future**

Silica v2 represents the culmination of extensive research into the world's highest-performance systems:

- **TigerBeetle's mechanical sympathy** for 1M+ TPS single-threaded performance
- **CockroachDB's distributed intelligence** for automatic load balancing
- **Sui's async coordination** for scalable cross-shard operations
- **Zero technical debt policy** for long-term maintainability

**Performance Target**: 1,000,000+ transactions per second with deterministic execution, automatic load balancing, and mathematical security guarantees.

**Next Action**: Begin implementing components following the mandatory coding patterns above. Every line of code should reflect our commitment to building the world's most efficient and reliable blockchain system.

Let's build something extraordinary! 🚀⚡

---

**Last Updated**: September 28, 2025  
**Performance Status**: Ready for 1M+ TPS implementation  
**Quality Standard**: TigerBeetle-inspired excellence
