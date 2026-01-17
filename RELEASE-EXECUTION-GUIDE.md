# The Rust Odyssey v1.0.0 - Release Execution Guide

**Status:** ✅ Git tag created and pushed to GitHub  
**Date:** January 17, 2026

---

## ✅ COMPLETED: Git Tagging and Push

The following has been executed:

```bash
# Tag created and pushed
git tag -a v1.0.0 -m "Release v1.0.0 - The Rust Odyssey: A C++ Developer's Journey"
git push origin main
git push origin v1.0.0
```

**Result:** Tag v1.0.0 is now live on GitHub at commit ba3fc35

---

## 📋 NEXT STEPS

### 1. Create GitHub Release

#### Option A: Using GitHub CLI (if installed)

```bash
gh release create v1.0.0 ^
  --title "The Rust Odyssey v1.0.0" ^
  --notes-file RELEASE-v1.0.0.md ^
  --latest
```

#### Option B: Manual via GitHub Web UI

1. Go to: https://github.com/robingkn/the-rust-odyssey/releases/new
2. **Choose a tag:** Select `v1.0.0` from dropdown
3. **Release title:** `The Rust Odyssey v1.0.0`
4. **Description:** Copy and paste entire contents of `RELEASE-v1.0.0.md`
5. Check ✅ **Set as the latest release**
6. Click **Publish release**

---

### 2. Generate Distribution Artifacts (PDF + EPUB)

**Prerequisites:** Install Pandoc and XeLaTeX

- **Pandoc:** https://pandoc.org/installing.html
- **XeLaTeX:** Install MiKTeX (Windows) or TeX Live

#### Create dist/ folder (already created)

```bash
mkdir dist
```

#### Generate PDF

```bash
pandoc book/00-title-page.md book/00-copyright.md book/00-preface.md book/00-how-to-read-this-book.md book/00-note-on-examples.md book/01-why-rust-exists.md book/02-hello-world-without-the-lies.md book/03-ownership-the-missing-concept.md book/04-borrowing-vs-aliasing.md book/05-lifetimes-without-the-fear.md book/06-raii-reimagined.md book/07-error-handling-without-exceptions.md book/08-traits-vs-templates.md book/09-zero-cost-abstractions.md book/10-unsafe-rust-for-cpp-veterans.md book/11-concurrency-without-data-races.md book/12-when-rust-feels-hard.md book/appendices/appendix-a-common-cpp-pitfalls.md book/appendices/appendix-b-reading-rust-compiler-errors.md book/appendices/appendix-c-mental-model-cheat-sheet.md book/99-acknowledgments.md book/99-about-the-author.md book/99-further-reading.md -o dist/the-rust-odyssey-v1.0.0.pdf --toc --toc-depth=2 --number-sections --pdf-engine=xelatex -V geometry:margin=1in -V documentclass=book -V papersize=letter --highlight-style=tango
```

#### Generate EPUB

```bash
pandoc book/00-title-page.md book/00-copyright.md book/00-preface.md book/00-how-to-read-this-book.md book/00-note-on-examples.md book/01-why-rust-exists.md book/02-hello-world-without-the-lies.md book/03-ownership-the-missing-concept.md book/04-borrowing-vs-aliasing.md book/05-lifetimes-without-the-fear.md book/06-raii-reimagined.md book/07-error-handling-without-exceptions.md book/08-traits-vs-templates.md book/09-zero-cost-abstractions.md book/10-unsafe-rust-for-cpp-veterans.md book/11-concurrency-without-data-races.md book/12-when-rust-feels-hard.md book/appendices/appendix-a-common-cpp-pitfalls.md book/appendices/appendix-b-reading-rust-compiler-errors.md book/appendices/appendix-c-mental-model-cheat-sheet.md book/99-acknowledgments.md book/99-about-the-author.md book/99-further-reading.md -o dist/the-rust-odyssey-v1.0.0.epub --toc --toc-depth=2 --number-sections --metadata title="The Rust Odyssey: A C++ Developer's Journey" --metadata author="Robin George Koshy" --metadata lang=en-US
```

**Expected Output:**
- `dist/the-rust-odyssey-v1.0.0.pdf`
- `dist/the-rust-odyssey-v1.0.0.epub`

---

### 3. Attach Artifacts to GitHub Release

#### Option A: Using GitHub CLI

```bash
gh release upload v1.0.0 dist/the-rust-odyssey-v1.0.0.pdf dist/the-rust-odyssey-v1.0.0.epub
```

#### Option B: Manual via Web UI

1. Go to: https://github.com/robingkn/the-rust-odyssey/releases
2. Find the v1.0.0 release
3. Click **Edit release**
4. Scroll to **Attach binaries** section
5. Drag and drop both PDF and EPUB files
6. Click **Update release**

---

## 💰 Leanpub Monetization Plan

### Setup Overview

**Platform:** Leanpub (https://leanpub.com)  
**Strategy:** GitHub remains canonical source, Leanpub provides paid convenience formats

### What to Upload

Create a `manuscript/` folder structure for Leanpub:

```
manuscript/
├── Book.txt              # Chapter order for full book
├── Sample.txt            # Free sample chapters
├── 00-title-page.md
├── 00-copyright.md
├── 00-preface.md
├── 00-how-to-read-this-book.md
├── 00-note-on-examples.md
├── 01-why-rust-exists.md
├── 02-hello-world-without-the-lies.md
├── 03-ownership-the-missing-concept.md
├── 04-borrowing-vs-aliasing.md
├── 05-lifetimes-without-the-fear.md
├── 06-raii-reimagined.md
├── 07-error-handling-without-exceptions.md
├── 08-traits-vs-templates.md
├── 09-zero-cost-abstractions.md
├── 10-unsafe-rust-for-cpp-veterans.md
├── 11-concurrency-without-data-races.md
├── 12-when-rust-feels-hard.md
├── appendices/
│   ├── appendix-a-common-cpp-pitfalls.md
│   ├── appendix-b-reading-rust-compiler-errors.md
│   └── appendix-c-mental-model-cheat-sheet.md
├── 99-acknowledgments.md
├── 99-about-the-author.md
└── 99-further-reading.md
```

#### Book.txt (Full book manifest)

```
00-title-page.md
00-copyright.md
00-preface.md
00-how-to-read-this-book.md
00-note-on-examples.md
01-why-rust-exists.md
02-hello-world-without-the-lies.md
03-ownership-the-missing-concept.md
04-borrowing-vs-aliasing.md
05-lifetimes-without-the-fear.md
06-raii-reimagined.md
07-error-handling-without-exceptions.md
08-traits-vs-templates.md
09-zero-cost-abstractions.md
10-unsafe-rust-for-cpp-veterans.md
11-concurrency-without-data-races.md
12-when-rust-feels-hard.md
appendices/appendix-a-common-cpp-pitfalls.md
appendices/appendix-b-reading-rust-compiler-errors.md
appendices/appendix-c-mental-model-cheat-sheet.md
99-acknowledgments.md
99-about-the-author.md
99-further-reading.md
```

#### Sample.txt (Free preview - first 3 chapters)

```
00-preface.md
00-how-to-read-this-book.md
00-note-on-examples.md
01-why-rust-exists.md
02-hello-world-without-the-lies.md
03-ownership-the-missing-concept.md
```

### Keeping GitHub as Canonical Source

**Recommended Workflow:**

1. **GitHub = Source of Truth**
   - All edits happen in GitHub repository
   - Version control via git tags
   - Public access remains free (CC BY-NC-SA)

2. **Sync to Leanpub**
   - **Option A (Recommended):** Use Leanpub's GitHub Integration
     - Connect repository in Leanpub settings
     - Map `book/` directory to Leanpub's `manuscript/`
     - Auto-generate on push to main branch
   - **Option B:** Manual sync
     - Copy `book/` contents to `manuscript/` folder
     - Upload to Leanpub when releasing updates
     - Trigger manual generation

3. **Update Workflow**
   - Make changes in GitHub
   - Tag new version (v1.0.1, v1.0.2, etc.)
   - Leanpub auto-syncs (or manual upload)
   - Leanpub notifies purchasers of updates

### Pricing Strategy

**Suggested Pricing:**
- **Minimum Price:** $9.99 (covers Leanpub fees + minimal revenue)
- **Suggested Price:** $19.99 (fair for 250-page technical book)
- **Reader-sets-price:** Yes (Leanpub allows pay-what-you-want above minimum)

**Rationale:**
- Technical books typically range $20-$40
- Specialized niche (C++ → Rust) justifies mid-range pricing
- No marketing budget = competitive pricing
- GitHub version remains free (non-commercial use)
- Leanpub purchasers pay for convenience (PDF/EPUB/MOBI) + support

**Free vs Paid:**
- **GitHub:** Free, CC BY-NC-SA 4.0 (non-commercial), Markdown format
- **Leanpub:** Paid, convenience formats (PDF/EPUB/MOBI), lifetime updates
- **Value Proposition:** Readers pay for polished formats and supporting the author

### Leanpub Setup Steps

1. **Create Account:** https://leanpub.com/author_getting_started
2. **Create New Book:**
   - Title: "The Rust Odyssey: A C++ Developer's Journey"
   - Subtitle: "Mental Models for C++ Developers Learning Rust"
   - Author: Robin George Koshy
3. **Connect GitHub Repository:**
   - Settings → Writing Mode → GitHub
   - Authorize Leanpub to access GitHub
   - Select repository: `robingkn/the-rust-odyssey`
   - Set manuscript folder: `book/`
   - Create `Book.txt` and `Sample.txt` in repository
4. **Configure Book Settings:**
   - Pricing: $9.99 minimum, $19.99 suggested
   - Sample: First 3 chapters (via Sample.txt)
   - License note: Mention GitHub version is CC BY-NC-SA
   - Categories: Programming, Rust, Systems Programming
5. **Generate Preview:**
   - Test PDF/EPUB generation
   - Review formatting and layout
   - Fix any Markdown compatibility issues
6. **Publish:**
   - Set book to "Published" status
   - Make available for purchase
   - Share link on GitHub README

### Free Sample Strategy

Offer first 3 chapters free to demonstrate value:
- Chapter 1: Why Rust Exists (sets context)
- Chapter 2: Hello World Without the Lies (shows honesty)
- Chapter 3: Ownership (core concept preview)

This gives C++ developers enough to evaluate if the book matches their needs.

---

## 📝 Post-Release Maintenance Policy

### Acceptable Changes (v1.0.x patch releases)

**Typo Fixes:**
- Spelling errors
- Grammar mistakes
- Punctuation errors
- Formatting inconsistencies

**Technical Corrections:**
- Factual errors about Rust or C++
- Incorrect code examples
- Compilation errors in examples
- Outdated compiler behavior (if Rust changes)

**Minor Improvements:**
- Broken links
- Clarifying confusing sentences
- Adding missing code comments
- Improving code formatting

**Process:**
- Accept via pull request or issue
- Verify and merge to main
- Tag as v1.0.1, v1.0.2, etc.
- Sync to Leanpub monthly (batch updates)
- No new GitHub release needed for patches

### Rejected Changes

**Scope Expansion:**
- ❌ New chapters
- ❌ New topics outside stated scope (async, macros, proc macros, etc.)
- ❌ Beginner content (basics of programming)
- ❌ Advanced topics not relevant to C++ developers
- ❌ Framework-specific content (web, game dev, etc.)

**Tone/Style Changes:**
- ❌ Marketing language or hype
- ❌ Advocacy or evangelism
- ❌ Overpromising Rust benefits
- ❌ Unfairly dismissing C++
- ❌ Adding humor or jokes
- ❌ Changing voice or personality

**Audience Changes:**
- ❌ Rewriting for beginners
- ❌ Rewriting for non-C++ developers
- ❌ Removing technical depth
- ❌ Simplifying for broader audience

**Response Template:**
> Thank you for the suggestion. This change falls outside the book's stated scope, which focuses on [specific scope]. The book is intentionally targeted at experienced C++ developers and maintains a specific voice and depth level. I appreciate your interest, but I won't be incorporating this change.

### Version Bumping Rules

**v1.0.x (Patch - Minor fixes)**
- Typos, grammar, formatting
- Technical corrections (code fixes)
- Broken links
- Clarifications without structural changes
- Updated examples for new Rust stable (if needed)
- **Release Cadence:** As needed, batch monthly

**v1.x.0 (Minor - Additive changes)**
- New appendix (if valuable)
- Expanded existing chapter (if gaps identified)
- Updated for new Rust edition (2024, 2027, etc.)
- Structural improvements (better chapter flow)
- Additional examples or diagrams
- **Release Cadence:** Yearly or as Rust editions release

**v2.0.0 (Major - Breaking changes)**
- New chapters (scope expansion)
- Target audience change
- Complete restructuring
- Significant rewrites
- Major scope changes
- **Release Cadence:** Only if fundamentally rethinking the book

### Maintenance Cadence

**Immediate (within 1 week):**
- Critical technical errors (wrong information)
- Broken code examples (won't compile)
- Factual mistakes about Rust or C++

**Monthly:**
- Batch typo fixes (collect and merge)
- Minor corrections
- Sync to Leanpub (trigger update)

**Yearly:**
- Rust edition updates (2024, 2027, etc.)
- Compiler behavior changes
- Standard library updates
- Review for outdated content

**As Needed:**
- Major Rust language changes
- Significant C++ standard updates (C++23, C++26)
- Community feedback on confusing sections

### Issue Triage Guidelines

When someone opens an issue:

1. **Typo/Grammar?**
   - Label: `typo`
   - Action: Accept, thank contributor, merge when ready
   - Timeline: Batch with other typos, merge monthly

2. **Technical Error?**
   - Label: `technical`
   - Action: Verify with compiler, test code
   - If confirmed: Accept and fix
   - If disputed: Request clarification or close
   - Timeline: Fix within 1 week if critical

3. **Scope Expansion?**
   - Label: `out-of-scope`
   - Action: Close politely, explain book's scope
   - Response: "Thank you, but this falls outside the book's focus on [X]"

4. **Tone/Style Change?**
   - Label: `wontfix`
   - Action: Close politely, explain editorial voice
   - Response: "The book maintains a specific voice for its target audience"

5. **Question?**
   - Label: `question`
   - Action: Answer helpfully, close when resolved
   - Consider: If many ask same question, might need clarification in text

6. **Suggestion?**
   - Label: `enhancement`
   - Action: Evaluate against scope and audience
   - Most likely: Close with explanation
   - Rarely: Accept if truly valuable and in-scope

### Pull Request Guidelines

**Accept:**
- Typo fixes (obvious errors)
- Code corrections (verified to compile)
- Broken link fixes
- Formatting improvements

**Review Carefully:**
- Technical corrections (verify accuracy)
- Clarifications (ensure they maintain voice)
- Code improvements (ensure they're better, not just different)

**Reject:**
- Scope expansion
- Style changes
- Rewrites for different audience
- Marketing language

**Response Time:**
- Typos: Accept within 1 week
- Technical: Review within 1 week, merge when verified
- Out-of-scope: Close within 3 days with explanation

---

## ✅ Release Completion Checklist

### Git Operations
- [x] Tag v1.0.0 created on HEAD
- [x] Tag pushed to GitHub
- [x] Main branch up to date

### GitHub Release
- [ ] Release created on GitHub (manual step required)
- [ ] Release notes from RELEASE-v1.0.0.md added
- [ ] Marked as latest release

### Distribution Artifacts
- [ ] Pandoc installed (prerequisite)
- [ ] PDF generated: `dist/the-rust-odyssey-v1.0.0.pdf`
- [ ] EPUB generated: `dist/the-rust-odyssey-v1.0.0.epub`
- [ ] Artifacts attached to GitHub release

### Leanpub Setup
- [ ] Leanpub account created
- [ ] Book created on Leanpub
- [ ] GitHub integration configured
- [ ] Book.txt and Sample.txt created
- [ ] Pricing set ($9.99 min, $19.99 suggested)
- [ ] Preview generated and reviewed
- [ ] Book published on Leanpub

### Documentation
- [x] Release notes complete (RELEASE-v1.0.0.md)
- [x] Maintenance policy documented
- [x] Leanpub plan documented
- [x] This execution guide created

---

## 🎯 Summary

**Completed:**
- Git tag v1.0.0 created and pushed to GitHub
- dist/ folder created
- Release execution guide documented

**Next Steps:**
1. Create GitHub release (manual via web UI or gh CLI)
2. Install Pandoc and generate PDF/EPUB
3. Attach artifacts to GitHub release
4. Set up Leanpub account and publish book

**Monetization:**
- GitHub: Free (CC BY-NC-SA)
- Leanpub: $9.99-$19.99 (convenience formats)

**Maintenance:**
- Accept: Typos, technical fixes
- Reject: Scope expansion, tone changes
- Version: v1.0.x for patches, v1.x.0 for minor, v2.0.0 for major

---

**The Rust Odyssey v1.0.0 is ready for public release!** 🚀
