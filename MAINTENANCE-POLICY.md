# Maintenance Policy - The Rust Odyssey v1.0.0

## Overview

This document defines what changes are accepted, rejected, and how versions are managed post-release.

---

## What We Accept

### 1. Typo Fixes (v1.0.x)
**Examples:**
- Spelling errors
- Grammar mistakes
- Punctuation errors
- Formatting inconsistencies

**Process:**
1. Open issue or submit pull request
2. Review and merge if valid
3. Tag as v1.0.x (patch version)
4. Sync to Leanpub monthly

**Timeline:** Batch monthly, unless critical

---

### 2. Technical Corrections (v1.0.x)
**Examples:**
- Factual errors about Rust or C++
- Incorrect code examples
- Outdated compiler behavior
- Broken links

**Process:**
1. Open issue with evidence (compiler output, docs link)
2. Verify with current Rust stable
3. Merge if confirmed incorrect
4. Tag as v1.0.x
5. Sync to Leanpub immediately if critical

**Timeline:** Immediate for critical, monthly for minor

---

### 3. Code Example Updates (v1.0.x)
**Examples:**
- Examples that no longer compile on stable Rust
- Deprecated API usage
- Compiler warnings in examples

**Process:**
1. Verify issue with current Rust stable
2. Update to idiomatic current syntax
3. Maintain same teaching point
4. Tag as v1.0.x
5. Document in changelog

**Timeline:** Quarterly review, or as reported

---

### 4. Clarifications (v1.x.0)
**Examples:**
- Confusing explanations (reported by multiple readers)
- Missing context that causes misunderstanding
- Better analogies or examples

**Process:**
1. Gather feedback (multiple reports preferred)
2. Propose clarification
3. Ensure it maintains voice and scope
4. Tag as v1.x.0 (minor version)
5. Document in changelog

**Timeline:** As needed, bundled with other changes

---

## What We Reject

### 1. Scope Expansion
**Examples:**
- New chapters on async/await
- Coverage of macros
- Embedded Rust content
- Web development topics

**Reason:** Book has defined scope (C++ → Rust mental models). New topics dilute focus.

**Response:** "Thank you for the suggestion. This topic is outside the book's scope, which focuses on core mental model translation for C++ developers. Consider this for a future book or separate resource."

---

### 2. Tone Changes
**Examples:**
- Adding marketing language
- Rust evangelism
- Dismissing C++ unfairly
- Overpromising Rust benefits

**Reason:** Book maintains calm, honest, non-evangelical tone.

**Response:** "The book intentionally maintains a neutral, factual tone. This change would alter the voice that readers expect."

---

### 3. Audience Changes
**Examples:**
- Simplifying for beginners
- Adding advanced topics for experts
- Removing C++ comparisons
- Explaining basic programming concepts

**Reason:** Target audience is experienced C++ developers (5-15+ years).

**Response:** "This book targets experienced C++ developers. This change would shift the audience focus."

---

### 4. Style Preferences
**Examples:**
- Rewriting in different voice
- Changing code formatting style
- Adding humor or jokes
- Removing technical depth

**Reason:** Consistent style and voice throughout.

**Response:** "This is a style preference. The book maintains consistent voice and formatting."

---

### 5. Subjective Opinions
**Examples:**
- "Rust is better than C++"
- "You should always use Rust"
- "C++ is obsolete"
- Personal language preferences

**Reason:** Book presents trade-offs, not advocacy.

**Response:** "The book presents trade-offs rather than advocacy. This change introduces subjective opinion."

---

## Version Bumping Rules

### v1.0.x (Patch)
**Triggers:**
- Typo fixes
- Grammar corrections
- Technical corrections
- Code example fixes
- Broken link fixes
- Formatting issues

**Impact:** No structural changes, no new content

**Frequency:** Monthly batches or immediate if critical

---

### v1.x.0 (Minor)
**Triggers:**
- New appendix (if needed)
- Significant clarifications
- Expanded existing sections (if confusion reported)
- Updated for new Rust edition (2024, 2027)
- Structural improvements

**Impact:** New content or significant changes, maintains scope

**Frequency:** Quarterly or as needed

---

### v2.0.0 (Major)
**Triggers:**
- New chapters
- Scope expansion
- Target audience change
- Complete restructuring
- Fundamental approach change

**Impact:** Significant changes to book structure or scope

**Frequency:** Only if warranted (years, not months)

---

## Issue Triage Process

### Step 1: Categorize
- **Typo/Grammar?** → Label `typo`, accept
- **Technical Error?** → Label `technical`, verify
- **Scope Expansion?** → Label `out-of-scope`, close politely
- **Tone/Style?** → Label `style`, close politely
- **Question?** → Label `question`, answer, close
- **Suggestion?** → Label `enhancement`, evaluate

### Step 2: Verify
- For technical issues: Test with current Rust stable
- For typos: Confirm error exists
- For suggestions: Evaluate against scope and audience

### Step 3: Respond
- Accept: Thank contributor, merge, tag version
- Reject: Explain reason politely, reference this policy
- Defer: Add to backlog for future consideration

### Step 4: Document
- Update CHANGELOG.md
- Tag version if merged
- Sync to Leanpub if needed

---

## Maintenance Cadence

### Immediate (within 1 week)
- Critical technical errors
- Code examples that don't compile
- Broken links to essential resources
- Factual mistakes about Rust/C++

### Monthly
- Batch typo fixes
- Minor corrections
- Sync to Leanpub
- Update CHANGELOG.md

### Quarterly
- Review code examples with latest Rust stable
- Check for deprecated APIs
- Review open issues
- Plan minor version updates

### Yearly
- Rust edition updates (2024, 2027, etc.)
- Major compiler behavior changes
- C++ standard updates (C++23, C++26)
- Comprehensive review

---

## Communication Templates

### Accepting Contribution
```
Thank you for catching this! This is a valid [typo/technical error/correction].
I'll merge this and include it in the next [patch/minor] release.
```

### Rejecting Scope Expansion
```
Thank you for the suggestion. This topic is outside the book's defined scope,
which focuses on core mental model translation for experienced C++ developers.
This would be great content for a separate resource or future book.
```

### Rejecting Tone Change
```
The book intentionally maintains a calm, factual, non-evangelical tone.
This change would alter the voice that readers have come to expect.
Thank you for understanding.
```

### Rejecting Style Preference
```
This appears to be a style preference rather than an error.
The book maintains consistent style and formatting throughout.
Thank you for the feedback.
```

---

## Quality Standards

### Before Merging
- [ ] Change maintains book's voice and tone
- [ ] Change fits target audience (experienced C++ devs)
- [ ] Code examples compile on stable Rust
- [ ] No scope creep
- [ ] Consistent formatting
- [ ] Links work correctly

### Before Tagging Version
- [ ] All merged changes reviewed
- [ ] CHANGELOG.md updated
- [ ] Version number follows semver
- [ ] Git tag created with message
- [ ] Pushed to GitHub

### Before Syncing to Leanpub
- [ ] GitHub release created
- [ ] All changes tested
- [ ] Preview generated and reviewed
- [ ] No formatting issues
- [ ] Ready for readers

---

## Long-Term Sustainability

### Goals
- Maintain quality over time
- Keep content accurate
- Respect reader expectations
- Avoid scope creep
- Sustainable maintenance effort

### Non-Goals
- Comprehensive Rust reference
- Covering every Rust feature
- Competing with official Rust book
- Frequent major updates
- Chasing every trend

### Success Metrics
- Code examples compile on stable Rust
- Readers report clarity and accuracy
- Minimal confusion or misunderstanding
- Sustainable maintenance workload
- Positive reader feedback

---

## Contact

- **Issues:** https://github.com/robingkn/the-rust-odyssey/issues
- **Pull Requests:** https://github.com/robingkn/the-rust-odyssey/pulls
- **Discussions:** GitHub Discussions (if enabled)

---

**Last Updated:** January 17, 2026  
**Version:** 1.0.0  
**Status:** Active
