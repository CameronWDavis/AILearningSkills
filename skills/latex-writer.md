--
name: latex-writing
description: Best practices for writing and reviewing semantically correct LaTeX — choosing the right list environment, cleveref cross-references, csquotes quotations, minted code, memoir floats, noweb literate programming (.nw), and dual beamer/article builds. Use this whenever writing, editing, refactoring, or reviewing .tex, .sty, .nw, or beamer source, whenever a document is being produced as LaTeX or PDF via LaTeX, and whenever debugging a LaTeX build error — even if the user only says "write this up", "fix my document", or "make notes" without naming LaTeX. Also use when converting notes, reports, or course material into LaTeX.
---
 
# LaTeX Writing Best Practices
 
LaTeX is a semantic markup system, not a word processor. Describe what content *is*; let LaTeX decide how it looks. Almost every rule below follows from that one principle.
 
## Lists: choose the environment that matches the content
 
### `description` for term–definition pairs
 
When items are labels followed by explanations, the label is structure, not styling:
 
```latex
\begin{description}
  \item[Term] Definition or explanation of the term
  \item[timeout] Maximum wait time in seconds
  \item[Passes] \verb|\documentclass{article}|
\end{description}
```
 
Never fake this with bold text inside `itemize`:
 
```latex
% WRONG
\begin{itemize}
  \item \textbf{timeout:} Maximum wait time in seconds
\end{itemize}
```
 
Typical cases: API parameters, configuration options, glossary entries, pass/fail examples, feature descriptions.
 
**Exception — pedagogical meta-commentary.** Instructor-facing annotations (\enquote{What varies}, \enquote{What stays invariant}, sequencing rationale) do not belong in a visible `description` list. Put them in `\ltnote{...}` (didactic-notes skill). Use `description` only for labelled content that is part of the student-facing document.
 
### `itemize` for uniform items, `enumerate` when order matters
 
Use `enumerate` for steps, rankings, and anything referred to by number.
 
### A single-item list is prose
 
One `\item` renders as an orphan bullet with nothing to enumerate. This happens constantly inside semantic environments, where a single question gets needlessly wrapped:
 
```latex
% BAD
\begin{exercise}
  \begin{itemize}
    \item Describe your algorithm for sorting laundry.
  \end{itemize}
\end{exercise}
 
% GOOD
\begin{exercise}
  Describe your algorithm for sorting laundry.
\end{exercise}
```
 
Two short sentences dressed as two bullets are usually also better as prose. Reach for a list only when there are genuinely multiple parallel, enumerable items.
 
### Never open an environment with a list or code block
 
Even a legitimate multi-item list must not be the first token inside `example`, `exercise`, `remark`, `definition`, or `block`. The environment sets an inline label (\enquote{Example 1.}) and the first list item or first code line overprints it. Lead with a sentence:
 
```latex
% BAD — enumerate collides with the "Example 1." label
\begin{example}[Making pancake batter]
  \begin{enumerate}
    \item Crack three eggs into a bowl.
  \end{enumerate}
\end{example}
 
% GOOD — a lead-in puts the list on its own line
\begin{example}[Making pancake batter]
  Follow these steps:
  \begin{enumerate}
    \item Crack three eggs into a bowl.
  \end{enumerate}
\end{example}
```
 
Same applies to `\inputminted` / `\begin{minted}`: precede it with something like \enquote{It looks like this:}.
 
The rule is not \enquote{no bullets} — bullets and code are fine once prose leads into them.
 
### Commentary between numbered items (enumitem `resume`)
 
When each enumerated item (survey questions, requirements, exam tasks) needs its own commentary, keep the `\item` text bare and put commentary as prose between resumed environments:
 
```latex
\begin{enumerate}
  \item \enquote{Have you programmed before this course?}
\end{enumerate}
Commentary motivating the question and what it serves.
\begin{enumerate}[resume]
  \item \enquote{How do you plan to study in this course?}
\end{enumerate}
```
 
Items sharing one commentary share one environment. Requires `enumitem`; in dual beamer/article builds load it in the article driver only (it conflicts with beamer's list internals) and keep such lists in article-mode prose.
 
Anti-pattern: appending the commentary inside the `\item` after the question — question and rationale then blur together.
 
### Recognition patterns
 
These all mean `description` was wanted:
 
- `\item \textbf{SomeLabel:}` → `\item[SomeLabel]`
- `\item \emph{SomeLabel:}` → `\item[SomeLabel]`
- `\item SomeLabel ---` → `\item[SomeLabel]`
- Any list where every item opens with bold or emphasized text
Also replace manual pseudo-lists with a real one:
 
```latex
% WRONG
\noindent\textbf{Configuration:} Set timeout to 30 seconds.\\
\textbf{Performance:} Optimized for large datasets.
 
% RIGHT
\begin{description}
  \item[Configuration] Set timeout to 30 seconds
  \item[Performance] Optimized for large datasets
\end{description}
```
 
## Cross-references
 
Use `\cref{...}` (cleveref) for every reference. cleveref supplies the correct prefix, handles pluralization, ranges, and language — hard-coding the word \enquote{Section} breaks all three.
 
```latex
% WRONG
Section~\ref{sec:intro} shows...
\S\ref{sec:background} discusses...
Figure~\ref{fig:plot} demonstrates...
 
% RIGHT
\cref{sec:intro} shows...
\cref{sec:background} discusses...
\cref{fig:plot} demonstrates...
\cref{sec:intro,sec:conclusion}   % "Sections 1 and 4"
```
 
Use descriptive labels: `\label{sec:introduction}`, not `\label{s1}`. Never hard-code a number.
 
## Quotations (csquotes)
 
Always `\enquote{...}`, never typed quote marks — `"..."`, ``` ``...'' ```, `'...'`. `\enquote` nests correctly and adapts to document language (Swedish »...«, English \enquote{...}).
 
For block quotes use `displayquote`.
 
### Cited quotation vs. attributed paraphrase
 
The command depends on whether the words are verbatim from the source.
 
**Verbatim** → use the integrated cited-quotation commands so the quotation carries its own reference. Do not write `\enquote{...}` plus a detached `\cite`/`\autocite`:
 
```latex
\textcquote[⟨prenote⟩][⟨postnote⟩]{key}{text}   % inline
\blockcquote[⟨postnote⟩]{key}{text}             % block
```
 
```latex
% WRONG
The study reports \enquote{much higher (63\%)} results \autocite[p.~167]{NCOL}.
% RIGHT
The study reports \textcquote[p.~167]{NCOL}{much higher (63\%)} results.
```
 
**Paraphrase, reconstructed dialogue, or a recounted example** is not a quotation. Attribute it integrally and keep any reported words in plain `\enquote`:
 
```latex
\Textcite[pp.~24--25]{NCOL} reports a study in which a child answers
\enquote{five} for one hand and \enquote{ten} for the other.
```
 
Plain `\enquote` otherwise: scare quotes, words-as-mention, back-references to an already-cited quotation.
 
**Crucial caveat:** `\textcquote`/`\blockcquote` assert the braced text appears verbatim in the source. Verify it does before wrapping it. Read the source and check before citing (backing-claims skill).
 
Use `\cite`/`\citep`/`\citet`/`\autocite` for citations — never type `[1]` or `(Smith 2020)` by hand.
 
## Emphasis
 
Never use ALL CAPITALS in running text. It reads as shouting, is harder to read, and encodes styling instead of meaning.
 
```latex
% WRONG
This is VERY important to understand.
% RIGHT
This is \emph{very} important to understand.
```
 
Use `\emph{...}`; for strong emphasis `\textbf{...}` or nested `\emph{\emph{...}}`. Acronyms and proper names conventionally capitalized (NASA, PDF) are fine and need no emphasis.
 
## Floats: figures and tables
 
An image is not a figure, but a figure can contain an image. Images go in a `figure` with a caption and a label.
 
### memoir: prefer `sidecaption`
 
```latex
\begin{figure}
  \begin{sidecaption}{Clear description of image content}[fig:label]
    \includegraphics[width=0.7\textwidth]{path/to/image}
  \end{sidecaption}
\end{figure}
 
\begin{table}
  \begin{sidecaption}{Description of table content}[tab:label]
    \begin{tabular}{lcc}
      \toprule
      Header1 & Header2 & Header3 \\
      \midrule
      Data1 & Data2 & Data3 \\
      \bottomrule
    \end{tabular}
  \end{sidecaption}
\end{table}
```
 
The caption sits alongside the content rather than above or below it, which uses page width better for narrow figures and keeps caption and content visually connected. The label goes in the optional second argument.
 
Use plain `\caption` + `\label` when not using memoir, when the float spans the full text width, or when the caption naturally belongs below (wide tables).
 
Reference floats with `\cref{fig:memory-hierarchy}` — never \enquote{Figure 1} or `Figure~\ref{...}`.
 
## Code and verbatim
 
Use `minted` (syntax-highlighted, needs `-shell-escape`) or `listings` for blocks; `\verb` or `\mintinline` inline. Never paste code as ordinary text.
 
**minted v3 (TeX Live 2024+) gotchas** — full detail in `references/minted-v3-and-floats.md` (search: `outputdir`, `Pygments lexer`):
 
- The `[outputdir=...]` package option was removed and is now an error. Load plain `\usepackage{minted}`.
- A `minted`/`\mintinline` language argument split across a line break fails with `Pygments lexer " python" is unknown`. Keep each invocation's arguments on one line; wrap prose *between* `\mintinline{...}{...}` calls, never inside one.
**`! LaTeX Error: Float(s) lost`** — fatal, reported without a location at `\end{document}`. In a dual beamer/article didactic+memoir build this is typically a citation or footnote inside a slide-only `\begin{frame}<presentation>` frame: the frame suppresses output but still executes its body, orphaning the margin footnote. Citations in ordinary frames are fine. See `references/minted-v3-and-floats.md` for diagnosis and the fragile-frame caveat.
 
## Paths
 
Always forward slashes: `figures/diagram.pdf`, never `figures\diagram.pdf`.
 
## Encoding and fonts
 
Unicode and font failures have *opposite* fixes on pdfLaTeX versus XeLaTeX/LuaLaTeX, so identify the engine first — `grep 'This is' file.log`. Build systems switch engines (`latexmk -pdf` vs `-xelatex`, Makefile rules tangled from a `.nw`), so re-check the log banner rather than assuming.
 
Full guidance in `references/unicode-and-fonts.md` (search: `DeclareUnicodeCharacter`, `iftex`, `newunicodechar`, `fontenc`, `pdffonts`). In brief:
 
- **pdfLaTeX**, `Unicode character … (U+XXXX) not set up` — add `\usepackage[utf8]{inputenc}` then `\DeclareUnicodeCharacter{XXXX}{...}` in the preamble, rather than editing the source.
- **XeLaTeX/LuaLaTeX** — that error cannot occur (native UTF-8) and `\DeclareUnicodeCharacter` is undefined. Guard pdfLaTeX-only mappings with `\ifpdftex … \fi` (iftex); remap glyphs with `\newunicodechar`.
- **pdfLaTeX**, code rendering as proportional serif — a T1-only monospace font (e.g. Bera Mono) needs `\usepackage[T1]{fontenc}`, loaded inside the `\ifpdftex` branch.
Diagnose rather than guess: `pdffonts -f N -l N file.pdf` shows which fonts a page embeds; `pdftotext -f N -l N` reveals the encoding through the glyph→Unicode mapping.
 
## Literate programming (.nw files)
 
In noweb files, quote code with `[[...]]`, not `\texttt` with hand-escaped underscores:
 
```latex
% WRONG
The \texttt{get\_submission()} method calls \texttt{\_\_getattribute\_\_}.
% RIGHT
The [[get_submission()]] method calls [[__getattribute__]].
```
 
`[[...]]` escapes special characters automatically, keeps the source readable, and prevents errors from forgotten escapes. Full rules, including the `\item[...]` bracket-collision trap, in `references/literate-programming.md`.
 
## Beamer and dual builds
 
For decks that also produce an article, and for single-source memoir + beamerarticle + beamer builds, read `references/dual-beamer-article.md` (search: `pyblock frame`, `ProvideSemanticEnv`, `restatable`, `biber root`). Core idea: slides need conciseness, articles need depth, so split verbose environments with `\mode<presentation>` and `\mode<article>` rather than compromising on one version that serves neither.
 
## Standard preamble
 
For new documents, copy `references/preamble.tex` verbatim to the project's `doc/preamble.tex` and include it after `\documentclass`:
 
```latex
\documentclass[a4paper,oneside]{memoir}
\input{preamble}
 
\title{Document Title}
\author{Author Name}
\date{\today}
 
\begin{document}
\frontmatter
\maketitle
\tableofcontents
 
\mainmatter
% Content here
 
\backmatter
\end{document}
```
 
The preamble supplies language support, bibliography, mathematics, quotations, code highlighting, noweb support, cross-references, tables, and common utilities (`enumitem`, `acro`, `siunitx`), so formatting stays consistent across projects.
 
## Workflow
 
1. **Check the file type.** In a `.nw` file, use `[[code]]`, not `\texttt{...\_...}`.
2. **Identify the content structure.** Uniform items, ordered steps, or term–definition pairs?
3. **Choose the matching semantic environment.**
4. **Use semantic commands** rather than manual formatting.
5. **Verify cross-references** — descriptive labels, `\cref` everywhere.
6. **Sweep for anti-patterns** before finishing.
## Review checklist
 
- [ ] Lists using `\textbf{Label:}` instead of `description`
- [ ] Single-item `itemize`/`enumerate` that should be prose
- [ ] A list or `minted` block as the opening token of a semantic environment
- [ ] Hard-coded numbers instead of `\ref`
- [ ] Manual cross-reference prefixes (`\S\ref`, `Section~\ref`, `Figure~\ref`) instead of `\cref`
- [ ] Manual citation formatting instead of `\cite` commands
- [ ] Manual quote marks instead of `\enquote{...}`
- [ ] Verbatim source text as `\enquote{...}` + detached `\cite`/`\autocite` instead of `\textcquote`/`\blockcquote` — and conversely, `\textcquote`/`\blockcquote` wrapping paraphrase that is not verbatim
- [ ] ALL CAPITALS used for emphasis
- [ ] Images without a `figure` environment
- [ ] Code without `minted`/`listings`/verbatim
- [ ] Windows-style backslashes in paths
- [ ] Engine-specific Unicode/font setup not guarded by `iftex`
- [ ] pdfLaTeX: non-ASCII characters without a `\DeclareUnicodeCharacter` mapping, or that command used without `\usepackage[utf8]{inputenc}`
- [ ] pdfLaTeX: monospace rendering as proportional serif — missing `\usepackage[T1]{fontenc}`
- [ ] Dual build: `pyblock` outside a both-modes `\begin{frame}[fragile]`, or literal \enquote{RQ1}/\enquote{H2} in prose instead of `\cref` to a semantic environment
Additional checks in `.nw` files:
 
- [ ] `\texttt{..._...}` or `\texttt{...__...}` → use `[[...]]`
- [ ] Description labels with underscores, `\item[FOO\_BAR behavior]` → better label plus `[[FOO_BAR]]` in the body
- [ ] Any manually escaped underscore in a code reference
## Reference files
 
- `references/literate-programming.md` — noweb `[[code]]` notation, when to use `\texttt` instead, `.nw` review patterns
- `references/minted-v3-and-floats.md` — minted v3 breaking changes and `Float(s) lost` diagnosis
- `references/unicode-and-fonts.md` — engine-dependent encoding and font fixes
- `references/dual-beamer-article.md` — presentation/article mode splitting, dual-build rules, mentipy polls
- `references/preamble.tex` — the standard preamble to copy into new projects
 
