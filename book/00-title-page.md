<!--
Title & cover handling for all formats.

PDF:
- Custom cover image
- Dedicated title page
- No headers/footers

HTML / EPUB:
- Title rendered via Markdown
- Metadata handled by Pandoc
-->

<!-- ========================= -->
<!-- PDF ONLY (LaTeX) SECTION -->
<!-- ========================= -->

```{=latex}
\thispagestyle{empty}

\begin{center}
\centering
\vspace*{2cm}
\includegraphics[width=0.7\textwidth]{assets/cover.png}
\end{center}

\newpage

\thispagestyle{empty}

\vspace*{4cm}

\begin{center}
\centering

{\Huge \textbf{The Rust Odyssey}}  
\vspace{0.8cm}

{\Large A C++ Developer's Journey}  
\vspace{1.5cm}

{\large Robin George Koshy}

\vfill

{\small Version 1.0.0}  
{\small January 2026}

\end{center}

\newpage
```

<!-- =============================== -->
<!-- HTML + EPUB FALLBACK (Markdown) -->
<!-- =============================== -->

# The Rust Odyssey
## A C++ Developer's Journey

**Robin George Koshy**

*Version 1.0.0*  
*January 2026*

---
