# optmodel

A LaTeX package for typesetting optimization models (MIP, LP, CP). Such a model features

* objective - min/max of some expression,
* constraints - aligned in columns (left-hand side, relation, right-hand side, quantifiers),
* optional per-line numbers or tags,
* and an optional tag for the whole model.

The model block can be centered or indented as a unit and always fits the line width.

Version 1.0 (2026/10/06) · Licence: [LPPL 1.3c](LICENSE) · Maintainer: Krtiiik

> **AI disclosure:** the original style file was written by the maintainer. Renaming and
> prefixing, bug fixes, the manual, this README and the packaging were done with AI
> assistance (Claude, by Anthropic). Design decisions and maintenance remain with the
> maintainer.

## Installation

`optmodel` needs a LaTeX kernel from 2020-10-01 or later (it uses
`\NewDocumentEnvironment`) and the packages `amsmath`, `mathtools` and `array`.

**Quick:** put `optmodel.sty` next to your `.tex` file.

**Per user (TeX Live / MiKTeX):** copy `optmodel.sty` to

```
<TEXMFHOME>/tex/latex/optmodel/optmodel.sty
```

(`kpsewhich -var-value TEXMFHOME` prints the folder; on MiKTeX, add the folder as a
root and refresh the file name database.) Copy `optmodel-doc.pdf` to
`<TEXMFHOME>/doc/latex/optmodel/` if you want `texdoc optmodel` to find it.

**Release zip:** each [release](../../releases) contains `optmodel.zip` in CTAN layout
(`optmodel/optmodel.sty`, `README.md`, `optmodel-doc.tex`, `optmodel-doc.pdf`).

## Interface

```latex
\usepackage{optmodel}

\begin{optmodel}[<model tag>][<label key>]
  <keyword> & <lhs> & <rel> & <rhs> & <quantifier> & <line tag> \\
  ...
\end{optmodel}
```

Both optional arguments may be left out. `[PP2]` prints the tag (PP2), `[]` takes the next
`equation` number, and the second argument is a `\label` key that `\ref` resolves to the tag.

| Command / environment | Purpose |
| --- | --- |
| `optmodel` | the environment |
| `\optobjective{..}` | objective row; starts at the lhs column and adds no width, so a long objective never widens the constraints |
| `\optwholerow{..}` | free row across the lhs, relation and rhs columns, left aligned (domains) |
| `\optnum` | auto-numbered line tag in the last column |
| `\opttag{..}` | fixed-text line tag in the last column; `\label` refers to the text |
| `\optcentertrue` / `\optcenterfalse` | centre the block (default) or indent it by `\optindent` |

| Length | Default | Meaning |
| --- | --- | --- |
| `\optkeysep` | 1em | keyword ↔ lhs |
| `\optquantsep` | 1em | rhs ↔ quantifier |
| `\optnumsep` | 2em | minimum gap before line numbers |
| `\opttagsep` | 1.5em | line numbers ↔ model tag |
| `\optrowsep` | 4pt | extra space between rows |
| `\optindent` | 2em | left indent when `\optcenterfalse` |

The manual `optmodel-doc.pdf` shows every mode.

## Example

```latex
\documentclass{article}
\usepackage{amssymb,optmodel}
\begin{document}
\begin{optmodel}[PP2][m:pp2]
  \min        & \optobjective{\sum_t c_t x_t} \\
  \text{s.t.} & \sum_t A^{\mathrm{I}}_t x_t     & \le & b^{\mathrm{I}}     &           & \opttag{I}\label{c:I} \\
              & A^{\mathrm{II}}_t x_t + B_t y_t & \le & b^{\mathrm{II}}_t, & \forall t & \opttag{II}\label{c:II} \\
              & \optwholerow{x_t \in \mathbf{X}_t,\ y_t \in \mathbf{Y}_t} & &
\end{optmodel}
\end{document}
```

## Limitations

- The body is read as an argument and typeset twice (the first pass measures it).
  Verbatim material (`\verb`, `listings`) is not allowed inside, and commands with
  side effects other than `\label`, `\optnum` and `\opttag` run twice.
- A model is one unbreakable block; it is not split across pages.
- Objectives are not line-broken. One wider than the space to the right of its column
  runs past the margin or into the model tag, and TeX does **not** report an overfull box.
  Check long objectives by eye and split them by hand.
- The layout has fixed six columns.
- With `hyperref` the package compiles cleanly and `\ref` prints the right text, but a
  `\label` after `\opttag` links to the enclosing section, not to the line. Model tags and
  `\optnum` lines link correctly.
- The environment builds on `equation*`; do not nest it inside other display environments.
- Cross-references need the usual second LaTeX run.
