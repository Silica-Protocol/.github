---
mode: agent
---
# Silica Protocol Project Agent Instructions

## Rust Best Practices (TigerBeetle-Inspired Excellence)
- Always use the latest stable Rust features and idioms.
- **ZERO TECHNICAL DEBT POLICY**: Do it right the first time, every time.
- Prioritize safety and security above all else:
  - No unsafe code unless absolutely necessary and justified.
  - Use strong typing, error handling, and input validation everywhere.
  - Avoid panics in production code; handle errors gracefully.
  - Use cryptographic libraries vetted by the Rust community.
  - Never hardcode secrets or credentials.
  - Always check for integer overflows, memory safety, and race conditions.
  - Use clippy and cargo audit to check for lints and vulnerabilities.
- **Performance & Memory Management Standards**:
  - **Static allocation only**: No dynamic allocation after startup
  - **64-byte cache alignment**: All hot data structures must be cache-aligned
  - **Bounded everything**: All loops, queues, collections have fixed upper bounds
  - **Batch operations**: Amortize costs through batching (network, disk, memory, CPU)
  - **Assert extensively**: Minimum 2 assertions per function (NASA Power of Ten Rules)
  - **Deterministic execution**: Byte-identical results across runs and replicas

## Security Requirements (Critical & Comprehensive)
- All external input (API, file, user, network) must be validated and sanitized.
- All cryptographic operations must use secure, up-to-date libraries.
- Never expose sensitive data in logs or error messages.
- Enforce rate limiting and anti-gaming measures for all reward logic.
- Always check for and handle potential denial-of-service vectors.
- **Resource exhaustion protection**: All operations must have bounded resource usage
- **Static memory safety**: Pre-allocate all memory at startup, no dynamic allocation
- **Deterministic validation**: All security checks must produce identical results
- **Fail-fast principle**: Use assertions to convert correctness bugs into liveness bugs
- **Input boundary validation**: Validate at system boundaries with multiple assertion layers

## Console Command Execution (WSL/Windows/Linux)
- **Never assume terminal output is available.**
- **Always redirect all command output (stdout and stderr) to a file.**
- **After running a command, read the output file from the filesystem to get results.**
- **Never try to read the terminal directly.**
- **Never assume there is no output; always check the log file.**
- Use shell redirection: `command 2>&1 | tee output.log` or `command > output.log 2>&1`.
- After every command, read the log file (e.g., `output.log`) to get the results.
- If a command fails, capture and report the error from the log file.

## High-Performance Coding Standards (TigerBeetle-Inspired)

### Memory Management Patterns
```rust
// 1. Cache-aligned structures for hot paths
#[repr(align(64))]
pub struct CacheAlignedData {
    hot_field: AtomicU64,
    buffer: [u8; 64],
}

// 2. Static memory pools (no dynamic allocation)
pub struct StaticResourceManager {
    connection_pool: StaticPool<Connection, 1000>,
    message_pool: RingBuffer<Message, 8192>,  // Power of 2 sizes
}

// 3. Bounded operations with defensive assertions
pub fn process_bounded<T>(items: &[T]) -> Result<Vec<Result<ProcessedItem, Error>>, Error> {
    const MAX_BATCH_SIZE: usize = 8192;  // TigerBeetle batch size
    
    assert!(items.len() <= MAX_BATCH_SIZE, "Batch exceeds static limit");
    assert!(!items.is_empty(), "Empty batch not allowed");
    
    let mut results = Vec::with_capacity(items.len());
    
    for (i, item) in items.iter().enumerate() {
        assert!(i < MAX_BATCH_SIZE, "Loop bound exceeded");
        results.push(process_item_deterministic(item)?);
    }
    
    assert_eq!(results.len(), items.len(), "Result count mismatch");
    Ok(results)
}
```

### Performance Requirements Checklist
- [ ] **Static allocation**: All memory pre-allocated at startup
- [ ] **Cache alignment**: Hot structures are 64-byte aligned  
- [ ] **Bounded operations**: All loops/queues have fixed upper bounds
- [ ] **Batch processing**: Operations batched for efficiency (8K+ items)
- [ ] **Zero-copy design**: Minimize memory copies in hot paths
- [ ] **Deterministic execution**: Identical results across runs
- [ ] **Direct I/O**: Use io_uring for storage operations
- [ ] **Assertions**: Minimum 2 assertions per function

## General Agent Guidance
- Always check for and report errors, warnings, and security issues.
- Document all assumptions and decisions in the output.
- When in doubt, prefer explicitness and safety over convenience.
- If a task involves code generation, always include tests and documentation.
- If a task involves running code, always output results to a file and read from the filesystem.
- If you encounter a WSL/Windows/Linux compatibility issue, document it and provide a workaround.
- **Apply TigerBeetle standards**: Every new component should follow mechanical sympathy principles
- **Zero technical debt**: Implement complete, production-ready solutions from the start

## Success Criteria (Zero Defect Standards)
- All code and actions must follow best Rust safety and security practices.
- All command results must be captured via file output, never from the terminal.
- All errors and warnings must be reported and handled.
- The solution must be robust, secure, and maintainable.
- **Performance standards**: Target TigerBeetle-level mechanical sympathy and efficiency
- **Memory standards**: Static allocation, cache alignment, zero-copy where possible
- **Testing standards**: Comprehensive test coverage with deterministic, reproducible results
- **Documentation standards**: Every public API documented with security considerations
- **Quality gate**: All code must pass clippy strict mode, security audits, and performance benchmarks

Always finish with a summary of actions taken, issues found, and recommendations for next steps.