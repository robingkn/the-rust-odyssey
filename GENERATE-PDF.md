# Generate PDF for The Rust Odyssey v1.0.0

## Issue

MiKTeX is missing required LaTeX packages. You need to install them first.

## Solution

### Option 1: Install Missing Packages (Recommended)

1. **Open MiKTeX Console** (search for "MiKTeX Console" in Windows)

2. **Enable automatic package installation:**
   - Go to Settings
   - Under "Package installation" select "Always install missing packages on-the-fly"
   - Click OK

3. **Run the pandoc command again:**
   ```bash
   pandoc book/00-title-page.md book/00-copyright.md book/00-preface.md book/00-how-to-read-this-book.md book/00-note-on-examples.md book/01-why-rust-exists.md book/02-hello-world-without-the-lies.md book/03-ownership-the-missing-concept.md book/04-borrowing-vs-aliasing.md book/05-lifetimes-without-the-fear.md book/06-raii-reimagined.md book/07-error-handling-without-exceptions.md book/08-traits-vs-templates.md book/09-zero-cost-abstractions.md book/10-unsafe-rust-for-cpp-veterans.md book/11-concurrency-without-data-races.md book/12-when-rust-feels-hard.md book/appendices/appendix-a-common-cpp-pitfalls.md book/appendices/appendix-b-reading-rust-compiler-errors.md book/appendices/appendix-c-mental-model-cheat-sheet.md book/99-acknowledgments.md book/99-about-the-author.md book/99-further-reading.md -o dist/the-rust-odyssey-v1.0.0.pdf --toc --toc-depth=2 --number-sections -V geometry:margin=1in -V documentclass=book -V papersize=letter --syntax-highlighting=tango
   ```

### Option 2: Manually Install Required Packages

1. **Open MiKTeX Console**

2. **Go to Packages tab**

3. **Search and install these packages:**
   - fancyvrb
   - framed
   - xcolor
   - hyperref
   - geometry
   - listings
   - caption
   - booktabs
   - longtable

4. **Run the pandoc command** (see above)

### Option 3: Update MiKTeX First

The error message says "you have not checked for MiKTeX updates". Try:

1. **Open MiKTeX Console**
2. **Click "Check for updates"**
3. **Install all updates**
4. **Enable automatic package installation** (Settings)
5. **Run the pandoc command** (see above)

## After PDF is Generated

Once the PDF is created successfully, upload it to the GitHub release:

```bash
# Check the PDF was created
dir dist

# Upload to GitHub release
gh release upload v1.0.0 dist/the-rust-odyssey-v1.0.0.pdf

# Commit the PDF to repository (optional)
git add dist/the-rust-odyssey-v1.0.0.pdf
git commit -m "build: add PDF distribution artifact"
git push origin main
```

## Expected Result

You should see:
- `dist/the-rust-odyssey-v1.0.0.pdf` (approximately 500-800 KB)
- PDF uploaded to GitHub release
- All three formats available: PDF, EPUB, HTML

## Troubleshooting

### If pandoc still fails:
1. Make sure MiKTeX bin directory is in PATH
2. Try running `pdflatex --version` to verify installation
3. Check MiKTeX Console for error messages
4. Install all recommended packages in MiKTeX

### If you want to skip PDF:
The EPUB and HTML formats are already available and sufficient for most readers. PDF is optional.

## Alternative: Use a Different Engine

If pdflatex continues to have issues, try with xelatex:

```bash
pandoc book/00-title-page.md book/00-copyright.md book/00-preface.md book/00-how-to-read-this-book.md book/00-note-on-examples.md book/01-why-rust-exists.md book/02-hello-world-without-the-lies.md book/03-ownership-the-missing-concept.md book/04-borrowing-vs-aliasing.md book/05-lifetimes-without-the-fear.md book/06-raii-reimagined.md book/07-error-handling-without-exceptions.md book/08-traits-vs-templates.md book/09-zero-cost-abstractions.md book/10-unsafe-rust-for-cpp-veterans.md book/11-concurrency-without-data-races.md book/12-when-rust-feels-hard.md book/appendices/appendix-a-common-cpp-pitfalls.md book/appendices/appendix-b-reading-rust-compiler-errors.md book/appendices/appendix-c-mental-model-cheat-sheet.md book/99-acknowledgments.md book/99-about-the-author.md book/99-further-reading.md -o dist/the-rust-odyssey-v1.0.0.pdf --pdf-engine=xelatex --toc --toc-depth=2 --number-sections -V geometry:margin=1in -V documentclass=book -V papersize=letter --syntax-highlighting=tango
```

---

**Once PDF is generated, you'll have all three formats ready for distribution!**
