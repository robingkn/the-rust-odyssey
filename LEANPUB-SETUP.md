# Leanpub Publishing Guide for The Rust Odyssey

## Quick Start

1. **Create Leanpub Account**
   - Go to: https://leanpub.com/author_getting_started
   - Sign up with your email

2. **Create New Book**
   - Click "Create a New Book"
   - Title: `The Rust Odyssey: A C++ Developer's Journey`
   - Subtitle: `Mental Models for C++ Developers Learning Rust`
   - Author: `Robin George Koshy`

3. **Connect GitHub Repository**
   - Go to Book Settings → Writing Mode
   - Select "GitHub"
   - Authorize Leanpub to access your GitHub
   - Repository: `robingkn/the-rust-odyssey`
   - Branch: `main`
   - Manuscript folder: `book/`

4. **Create Book.txt Manifest**
   
   Create `book/Book.txt` with this content:
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

5. **Create Sample.txt for Free Preview**
   
   Create `book/Sample.txt` with this content:
   ```
   00-preface.md
   00-how-to-read-this-book.md
   00-note-on-examples.md
   01-why-rust-exists.md
   02-hello-world-without-the-lies.md
   03-ownership-the-missing-concept.md
   ```

6. **Configure Pricing**
   - Go to Book Settings → Pricing
   - Minimum Price: $9.99
   - Suggested Price: $19.99
   - Enable "Reader Sets Price": Yes

7. **Set Book Details**
   - Category: Programming → Systems Programming
   - Tags: Rust, C++, Systems Programming
   - Language: English
   - About the Book: (Copy from README.md)

8. **Generate Preview**
   - Click "Preview" to test PDF/EPUB generation
   - Review formatting and layout
   - Fix any issues in markdown files

9. **Publish**
   - Click "Publish" when ready
   - Book will be available at: `https://leanpub.com/the-rust-odyssey`

## Workflow: GitHub as Source of Truth

### Making Updates

1. Edit files in GitHub repository
2. Commit and push to `main` branch
3. Go to Leanpub dashboard
4. Click "Create Preview" or "Publish New Version"
5. Leanpub pulls latest from GitHub and regenerates

### Version Management

- **GitHub**: Source of truth, version control
- **Leanpub**: Distribution platform, auto-syncs from GitHub
- **Releases**: Tag versions in GitHub (v1.0.0, v1.0.1, etc.)
- **Leanpub Updates**: Publish new version after each GitHub release

## Pricing Strategy

### Rationale
- **Free on GitHub**: CC BY-NC-SA license, read online
- **Paid on Leanpub**: Convenience formats (PDF, EPUB, MOBI)
- **Price Point**: $9.99-$19.99 (fair for technical content)
- **No Marketing**: Lean approach, word-of-mouth only

### Comparison
- Technical books: $20-$40 typically
- Specialized niche: C++ → Rust
- ~250 pages of content
- No marketing budget = lower price

### Value Proposition
- Readers pay for convenience, not content
- Support author's work
- Get formatted PDF/EPUB/MOBI
- Automatic updates when book is revised

## License Compatibility

- **GitHub**: CC BY-NC-SA 4.0 (text), MIT (code)
- **Leanpub**: Commercial distribution allowed
- **Compatibility**: ✅ CC BY-NC-SA allows commercial use with attribution
- **Attribution**: Maintained in copyright page

## Marketing (Minimal)

### Launch Announcement
- Post on personal blog/social media
- Share GitHub link (free version)
- Mention Leanpub for paid formats
- No aggressive marketing

### Ongoing
- Let quality speak for itself
- Encourage reviews/feedback
- Accept corrections via GitHub issues
- Word-of-mouth only

## Support & Updates

### Reader Support
- GitHub Issues: Technical questions, corrections
- Email: Direct support (if needed)
- Leanpub Comments: Reader feedback

### Update Cadence
- **Immediate**: Critical errors
- **Monthly**: Batch typo fixes, sync to Leanpub
- **Yearly**: Rust edition updates
- **As Needed**: Major language changes

## Revenue Expectations (Realistic)

### Lean Approach
- No marketing = slow organic growth
- Niche audience = limited reach
- Quality content = word-of-mouth
- Realistic: 10-50 sales/month initially

### Long Tail
- Technical books have long shelf life
- Rust adoption growing
- Steady trickle over years
- Focus on quality, not quick profit

## Next Steps

1. ✅ GitHub repository ready (v1.0.0 tagged)
2. ✅ Distribution artifacts generated (EPUB, HTML)
3. ⏳ Create Book.txt and Sample.txt
4. ⏳ Create Leanpub account
5. ⏳ Connect GitHub repository
6. ⏳ Generate preview
7. ⏳ Publish

---

**Note**: This is a lean, sustainable approach. No pressure, no hype, just good technical content for the right audience.
