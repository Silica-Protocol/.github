# Definition of "Done" - Completion Criteria

**MANDATORY**: This document defines what "complete" means for the Chert/Silica project.

---

## 🚨 CRITICAL RULE: No Incomplete Work Marked as Complete

**If your code contains ANY of these, it is NOT complete:**

### ❌ Stub Indicators (Automatic Disqualification)
- `// for now` - Temporary solution marker
- `// For now` - Same as above (case insensitive)
- `// TODO:` - Work remains to be done
- `// FIXME:` - Something needs fixing
- `// HACK:` - Non-ideal solution
- `// TEMP:` - Temporary code
- `// XXX:` - Warning marker
- `// STUB:` - Placeholder implementation
- `// PLACEHOLDER:` - Not real implementation
- `// INCOMPLETE:` - Explicitly incomplete
- `in production this would` - Admitting current code isn't production-ready
- `simplified for now` - Admitting simplification
- `disabled for now` - Admitting functionality disabled

### ❌ Implementation Indicators
- Functions that just `log!()` and return without acting
- Functions returning hardcoded `0`, `None`, `Ok(())`, `vec![]` as placeholders
- Empty `match` arms with `// TODO`
- Commented-out code that should be active
- Disabled tests with `#[ignore]` that should pass
- Mock/fake data instead of real data sources
- Simplified algorithms with promises of "real implementation later"

---

## ✅ Definition of COMPLETE

Work can ONLY be marked complete when ALL of the following are true:

### 1. Full Implementation ✅
- [ ] All functionality implemented (no stubs)
- [ ] All edge cases handled
- [ ] All error cases handled gracefully
- [ ] No placeholder values or mock data
- [ ] No disabled features that should be enabled
- [ ] No "simplified" versions of algorithms
- [ ] All optimizations that were promised are implemented

### 2. Testing ✅
- [ ] All unit tests written and passing
- [ ] All integration tests written and passing
- [ ] Test coverage ≥ 90% for new code
- [ ] All edge cases tested
- [ ] All error cases tested
- [ ] Performance tests passing
- [ ] Security tests passing
- [ ] No `#[ignore]` on tests (unless legitimately flaky/slow and documented)

### 3. Documentation ✅
- [ ] All public APIs documented with rustdoc
- [ ] All complex logic explained with comments
- [ ] Usage examples provided
- [ ] Security considerations documented
- [ ] Performance characteristics documented
- [ ] Breaking changes documented
- [ ] Migration guide if applicable

### 4. Code Quality ✅
- [ ] `cargo fmt` passes
- [ ] `cargo clippy -- -D warnings` passes
- [ ] `cargo audit` passes (no vulnerabilities)
- [ ] No `unwrap()` or `expect()` in production code
- [ ] All TODO/FIXME/HACK comments removed
- [ ] All "for now" comments removed
- [ ] Code review approved

### 5. Security ✅
- [ ] Security review completed
- [ ] No hardcoded secrets
- [ ] All inputs validated
- [ ] All cryptographic operations use approved libraries
- [ ] No unsafe code (or justified and reviewed)
- [ ] Threat model documented

### 6. Performance ✅
- [ ] Performance benchmarks pass
- [ ] No performance regressions
- [ ] Memory usage within bounds
- [ ] Static memory allocation verified (TigerBeetle standard)
- [ ] Hot paths optimized

---

## 📋 Status Definitions

### "not-started"
- Work has not been begun
- No code written yet

### "in-progress"
- Actively being worked on, OR
- Contains any TODO/FIXME/HACK comments, OR
- Has "for now" temporary solutions, OR
- Has incomplete implementations, OR
- Tests not complete, OR
- Documentation not complete

**CRITICAL**: If you add a TODO comment, status becomes "in-progress"

### "blocked"
- Cannot proceed due to external dependency
- Blocked by another task or team
- Waiting for decision/approval
- Must document blocker and expected resolution

### "review"
- Implementation complete (meets all criteria above)
- Submitted for peer review
- Awaiting approval

### "complete"
- All criteria met
- Code reviewed and approved
- Tests passing in CI
- Documentation complete
- Zero TODOs/FIXMEs in the completed code
- Ready for production deployment
- **Can be merged to main**

---

## 🚫 Common Mistakes - Do NOT Do This

### ❌ Mistake 1: Marking Complete with TODOs
```rust
// ❌ WRONG - This is NOT complete!
pub fn transfer_tokens(from: &str, to: &str, amount: u64) -> Result<()> {
    // TODO: Implement actual token transfer
    Ok(())
}
```
**Status:** ❌ "complete" - WRONG! Should be "in-progress"

### ❌ Mistake 2: Marking Complete with "For Now"
```rust
// ❌ WRONG - This is NOT complete!
pub fn select_committee() -> Vec<Validator> {
    // For now, use round-robin selection
    // In production, this should use VRF
    round_robin_select()
}
```
**Status:** ❌ "complete" - WRONG! Should be "in-progress"

### ❌ Mistake 3: Marking Complete with Stubs
```rust
// ❌ WRONG - This is NOT complete!
pub fn verify_signature(&self, msg: &[u8], sig: &[u8]) -> Result<bool> {
    warn!("Signature verification not implemented");
    Ok(true) // Placeholder
}
```
**Status:** ❌ "complete" - WRONG! Should be "in-progress"

---

## ✅ Correct Examples

### ✅ Example 1: Truly Complete Function
```rust
/// Transfer tokens from one account to another.
/// 
/// # Security
/// - Validates all inputs
/// - Checks balances atomically
/// - Uses proper error handling
/// 
/// # Errors
/// Returns error if balance insufficient or accounts invalid
pub fn transfer_tokens(
    &mut self,
    from: &AccountId,
    to: &AccountId,
    amount: u64,
) -> Result<TransactionReceipt> {
    // Input validation
    assert!(!from.is_empty(), "From account cannot be empty");
    assert!(!to.is_empty(), "To account cannot be empty");
    assert!(amount > 0, "Amount must be positive");
    
    // Get accounts with lock
    let mut from_account = self.get_account_mut(from)
        .context("From account not found")?;
    let mut to_account = self.get_account_mut(to)
        .context("To account not found")?;
    
    // Check balance
    if from_account.balance < amount {
        return Err(anyhow!("Insufficient balance"));
    }
    
    // Perform transfer atomically
    from_account.balance -= amount;
    to_account.balance += amount;
    
    // Create receipt
    let receipt = TransactionReceipt {
        from: from.clone(),
        to: to.clone(),
        amount,
        timestamp: Utc::now(),
        tx_id: generate_tx_id(),
    };
    
    // Emit event
    self.emit_event(Event::Transfer(receipt.clone()));
    
    Ok(receipt)
}

#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_transfer_success() {
        // Test successful transfer
    }
    
    #[test]
    fn test_transfer_insufficient_balance() {
        // Test error case
    }
    
    #[test]
    fn test_transfer_invalid_accounts() {
        // Test validation
    }
}
```
**Status:** ✅ "complete" - All criteria met!

---

## 🔍 How to Check Before Marking Complete

### Automated Checks (MUST PASS)
```bash
# Run all checks
./scripts/verify_completion.sh

# Or manually:
cargo fmt --all --check
cargo clippy --all-targets --all-features -- -D warnings
cargo test --workspace --all-features
cargo audit

# Check for stub markers
grep -r "TODO:" src/
grep -r "FIXME:" src/
grep -ri "for now" src/
grep -r "// HACK" src/
```

If ANY of these find matches in your "completed" code, it's NOT complete!

### Manual Review Checklist
- [ ] No TODO comments in completed code
- [ ] No "for now" comments in completed code
- [ ] No placeholder implementations
- [ ] All tests written and passing
- [ ] Documentation complete
- [ ] Security review passed
- [ ] Performance benchmarks met
- [ ] Peer review approved

---

## 📊 Tracking Progress

### Good Progress Tracking
```markdown
## Feature: Token Transfers

### Status: in-progress (60% complete)

#### Completed ✅
- [x] Basic transfer function
- [x] Balance validation
- [x] Unit tests for happy path

#### In Progress 🔄
- [ ] Atomic transaction handling
- [ ] Event emission
- [ ] Error recovery

#### Not Started ⏳
- [ ] Multi-signature support
- [ ] Transaction receipts
- [ ] Performance optimization
```

### Bad Progress Tracking ❌
```markdown
## Feature: Token Transfers

### Status: complete ✅  ← WRONG! Still has TODOs below!

- [x] Basic transfer function (TODO: Add atomicity)  ← Has TODO!
- [x] Balance validation (simplified for now)  ← "for now" = incomplete!
- [x] Tests (TODO: Add edge cases)  ← Has TODO!
```

---

## 🎯 Bottom Line

### The Golden Rule:
**If you write "TODO" or "for now", mark the task as "in-progress", not "complete".**

### The Test:
**Ask yourself: "Would I ship this to production tomorrow?"**
- If NO → Status: "in-progress"
- If YES → Status: "complete"

### The Commitment:
**We ship quality, not velocity.**
- Better to have 10 truly complete features than 100 "complete" features with TODOs
- Better to be honest about progress than to inflate completion metrics
- Better to take longer and ship solid code than to rush and accumulate debt

---

**This document is MANDATORY for all contributors.**

Last Updated: 2025-10-02
