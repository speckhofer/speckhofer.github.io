---
layout: post
title: "LaTeX auto activating snippets in Emacs"
subtitle: "An efficient LaTeX setup for Emacs using auto-expanding snippets"
background: "/assets/img/background.jpg"
typora-root-url: ".."
math: true
---

This page contains an overview of my $\LaTeX$ setup in Emacs. It enables one to write LaTeX code in a very convenient and fast way (sufficiently fast to take notes in real time during lectures). This is achieved by using auto-expanding snippets. In order to expand such a snippet, it is not necessary to press an extra key such as tab or enter---instead, it gets triggered immediately after typing a specific key sequence, without interrupting the flow of typing. The setup is inspired by [this article by Gilles Castel](https://castel.dev/post/lecture-notes-1/), where an analogous setup for Vim is described. It is based on the [laas](https://github.com/emacsmirror/laas) and [CDLaTeX](https://github.com/cdominik/cdlatex) packages, with some modifications and lots of additional snippets.

<center><img src="/assets/img/2025-05-18-latex-auto-activating-snippets-in-emacs/latex-auto-activating-snippets-in-emacs.png" alt="LaTeX auto activating snippets in Emacs" width="100%"/></center>

**Contents:**

- TOC
{:toc}
---

## Overview and usage

As explained above, the main purpose of this setup is that it provides using auto-activating snippets to speed up and simplify typing $\LaTeX$ code. For a detailed expanation on what auto-activating snippets are and why they are useful, I recommend reading the aforementioned [article by Gilles Castel](https://castel.dev/post/lecture-notes-1/). Here are some examples of the most useful snippets which are triggered automatically while typing in this setup:

- `mk` is replaced by `$$` (math mode), and the cursor is placed inbetween the dollar signs.

- `dm` is replaced by `\begin{equation*} \end{equation*}` (display-math mode).

- `<=` is replaced by `\leq`, and `>=` is replaced by `\geq`, `=>` is replaced by `\implies`.

- Greek letters: `.a` is replaced by `\alpha`, `.b` by `\beta`, `.e` by `\epsilon`, `..e` by `\varepsilon`, `.G` by `\Gamma` etc.

- `sin`, `cos`, `lim`, `int`, `sum` etc. are replaced by `\sin`, `\cos`, `\lim`, `\int`, `\sum` etc.

- `sr` ("squared") is replaced by `^2`, `cb` by `^3` ("cubed"), `td` by `^{}`, and `inv` by `^{-1}`.

- `oo` is replaced by `\infty`, `...` by `\dots`, `**` is replaced by `\cdot` and `xx` is replaced by `\times`.

- `//` is replaced by `\frac{}{}`, the cursor is placed in the first curly brackets pair and you can use the `tab` key to jump into the second pair and to exit the brackets.

- `xbar` is replaced by `\bar{x}`, and there are analogous snippets for `yhat`, `ztil` ("tilde") etc.

- `lr(` is replaced by `\Bigl(  \Bigr)` (and you can use `tab` to navigate), `norm` is replaced by `\|  \|` etc, `set` is replaced by `\{ \}` etc.

- `NN` is replaced by `\mathbb{N}`, analogously for `ZZ`, `QQ`, `RR` etc.

- `UU` is replaced by `\cup` (union), `Nn` by `\cap` (intersection), `WW` by `\wedge`, `VV` by `\vee`, `uuu` by `\bigcup` etc.

- `AA` is replaced by `\forall`, `EE` is replaced by `\exists`.

- Automatic subscripts: `x0` is replaced by `x_0`, `a10` by `a_{10}`, `f_` by `f_{}`. For some letters (such as `n`), there are snippets like `xnn`, which is replaced by `x_n`.

A complete list of all snippets can be found below. Moreover, these snippets can be edited in the file [laas.el](/assets/files/2025-05-18-latex-auto-activating-snippets-in-emacs/laas.el).

Thus, for example, you can press the following key sequence:
```
exp(x) = sum_n=0[tab]tdoo[tab] //xtdn[tab]n!
```
(where `[tab]` stands for the `tab` key), and without pressing any other keys to activate the snippets, this is automatically replaced by the following LaTeX code:

```latex
\exp(x) = \sum_{n=1}^{\infty} \frac{x^{n}}{n!}
```
Similarly, you can press the following key sequence:
```
AA..e>0EEn0innNNAAn>=n0: normxnn-x[tab]<..e
```
which results in the following LaTeX code:
```latex
\forall \varepsilon > 0 \exists n_0\in \mathbb{N}\forall n\geq n_0: \| x_n - x \|<\varepsilon
```

Clearly, using these snippets has the potential of saving quite some time, and arguably, it is also much more convenient than typing all the backslashes which are needed when typing the code manually.


## Installation

In order to install this setup, proceed as follows:

1. Install Emacs. I am using [Doom Emacs](https://github.com/doomemacs/doomemacs) and using the "Evil" mode for Vim-like navigation, but the setup should work analogously in other Emacs configurations.

1. Set up $\LaTeX$ support in Emacs, e.g., by uncommenting the `latex` entry in your `init.el` file in Doom Emacs.

1. Install the CDLaTeX package. In Doom Emacs, this can be done by adding the following to your `init.el` file (restart Emacs or execute the doom/reload command after installing a new package).
   ```elisp
          (latex             ; writing papers in Emacs has never been so fun
           +cdlatex)
   ```

1. Download the file [laas.el](/assets/files/2025-05-18-latex-auto-activating-snippets-in-emacs/laas.el) and put it into the directory `~/.emacs.d/lisp/` (create it if it doesn't exists).

1. Add the following code to your `config.el` file. The first lines are important if you want to use `jk` and `kl` as your evil-escape-key-sequence. Usually, this would interfere with snippets containing the letter `k` (due to some small delay), but using the key-chord package fixes this issue. Next, the packages CDLaTeX and aas ("Auto Activating Snippets") are loaded, and some snippets are added to the aas package. These are triggered at any point in text, not only in math modes. Finally, the file `laas.el` is loaded, containing those LaTeX snippets which are only triggered in math environments.
   ```elisp
   (require 'key-chord)
   (key-chord-mode 1)
   (key-chord-define evil-insert-state-map  "jk" 'evil-normal-state)
   (key-chord-define evil-visual-state-map "jk" 'evil-normal-state)
   (key-chord-define evil-insert-state-map  "kj" 'evil-normal-state)
   (key-chord-define evil-visual-state-map "kj" 'evil-normal-state)

   (require 'latex)
   (require 'cdlatex)

   (after! latex
     (define-key LaTeX-mode-map [tab] 'cdlatex-tab))
   (add-hook 'LaTeX-mode-hook 'cdlatex-mode)

   (after! cdlatex
     (setq cdlatex-math-modify-prefix "f7") ;; Use f7 instead of '
     (define-key LaTeX-mode-map "'" nil)
     (define-key cdlatex-mode-map "'" nil))

   (use-package aas
     :hook (LaTeX-mode . aas-activate-for-major-mode)
     :hook (org-mode . aas-activate-for-major-mode)
     :config
     (aas-set-snippets 'text-mode
       ;; expand unconditionally
           "km" (lambda () (interactive)
            (yas-expand-snippet "\$$0\$"))
           "dm" (lambda () (interactive)
            (yas-expand-snippet "\\begin{equation*}\n  $0\n\\end{equation*}"))
            ;; alternative snippet:
            ;; (yas-expand-snippet "\\[\n  $0\n\\]"))
           "alig" (lambda () (interactive)
             (yas-expand-snippet "\\begin{align*}\n  $0\n\\end{align*}"))
           "eqn" (lambda () (interactive)
             (yas-expand-snippet "\\begin{equation}\n  $0\n\\end{equation}"))
           "cite" (lambda () (interactive)
             (yas-expand-snippet "\\cite{$0}"))
           "cref" (lambda () (interactive)
             (yas-expand-snippet "\\Cref{$0}"))
           "eqref" (lambda () (interactive)
             (yas-expand-snippet "~\\eqref{$0}"))
           "latex"  "\\LaTeX"
           "TEMPLATE" (lambda () (interactive)
             (yas-expand-snippet "\\documentclass[12pt,a4paper,oneside]{article}\n\\usepackage[utf8]{inputenc}\n\\usepackage[T1]{fontenc}\n\\usepackage{lmodern}\n\\usepackage[left=2.5cm,right=2.5cm,top=2.5cm,bottom=2.5cm,headsep=1.2cm]{geometry}\n\\usepackage{amsmath, amsfonts, amssymb, amsthm, graphicx, tikz, float, pdfpages, enumerate, mathrsfs, hyperref}\n%=========================================================\n\n\\begin{document}\n\n$0\n\n\\end{document}\n\n"))))

   (add-to-list 'load-path "~/.emacs.d/lisp/")
   (load "laas.el")
   (add-hook 'LaTeX-mode-hook 'laas-mode)
   (add-hook 'org-mode-hook 'laas-mode)

   (use-package laas
     :config
     (aas-set-snippets 'laas-mode
       :cond #'texmathp ; expand only while in math. You can add some snippets here.
       "+dint" (lambda () (interactive)
               (yas-expand-snippet "\\int_{$1}^{$2} $0"))
   ```
  
1. Execute the command `doom/reload` and restart Emacs.

1. To edit the snippets, you can modify the file `laas.el` or change the lines which you have added to `config.el`.


## Snippets list

Here you can find an overview of all snippets which are available after setting everything up as described above.

| Snippet    | Expands to                                           | Remark                                                                                                                                                                                              |
| ---------- | ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `km`       | `$$`                                                 |                                                                                                                                                                                                     |
| `dm`       | `\begin{equation*}`<br/>`\end{equation*}`            |                                                                                                                                                                                                     |
| `alig`     | `\begin{align*}`<br/>`\end{align*}`                  |                                                                                                                                                                                                     |
| `eqn`      | `\begin{equation}`<br/>`\end{equation}`              |                                                                                                                                                                                                     |
| `cite`     | `\cite{}`                                            |                                                                                                                                                                                                     |
| `cref`     | `\Cref{}`                                            | If not using the Cleveref package, replace this by `\ref{}`.                                                                                                                                        |
| `eqref`    | `~\eqref{}`                                          |                                                                                                                                                                                                     |
| `latex`    | `\LaTeX`                                             |                                                                                                                                                                                                     |
| `TEMPLATE` | inserts a LaTeX template                       |                                                                                                                                                                                                     |
| `!=`       | `\ne `                                               |                                                                                                                                                                                                     |
| `==`       | `&= `                                                |                                                                                                                                                                                                     |
| `equiv`    | `\equiv `                                            |                                                                                                                                                                                                     |
| `app`      | `\approx `                                           |                                                                                                                                                                                                     |
| `sim`      | `\sim `                                              |                                                                                                                                                                                                     |
| `>=`       | `\ge `                                               |                                                                                                                                                                                                     |
| `ge`       | `\ge `                                               |                                                                                                                                                                                                     |
| `<=`       | `\le `                                               |                                                                                                                                                                                                     |
| `le`       | `\le `                                               |                                                                                                                                                                                                     |
| `>>`       | `\gg `                                               |                                                                                                                                                                                                     |
| `<<`       | `\ll `                                               |                                                                                                                                                                                                     |
| `+-`       | `\pm `                                               |                                                                                                                                                                                                     |
| `-+`       | `\mp `                                               |                                                                                                                                                                                                     |
| `**`       | `\cdot `                                             |                                                                                                                                                                                                     |
| `xx`       | `\times `                                            |                                                                                                                                                                                                     |
| `sr`       | `^2`                                                 |                                                                                                                                                                                                     |
| `cb`       | `^3`                                                 |                                                                                                                                                                                                     |
| `EE`       | `\exists `                                           |                                                                                                                                                                                                     |
| `AA`       | `\forall `                                           |                                                                                                                                                                                                     |
| `iff`      | `\iff `                                              |                                                                                                                                                                                                     |
| `=>`       | `\implies `                                          |                                                                                                                                                                                                     |
| `=<`       | `\impliedby `                                        |                                                                                                                                                                                                     |
| `lrar`     | `\leftrightarrow`                                    |                                                                                                                                                                                                     |
| `->`       | `\to `                                               |                                                                                                                                                                                                     |
| `<-`       | `\gets `                                             |                                                                                                                                                                                                     |
| `mapsto`   | `\mapsto `                                           |                                                                                                                                                                                                     |
| `inn`      | `\in `                                               |                                                                                                                                                                                                     |
| `notin`    | `\notin`                                             |                                                                                                                                                                                                     |
| `...`      | `\dots`                                              |                                                                                                                                                                                                     |
| `oo`       | `\infty`                                             |                                                                                                                                                                                                     |
| `::`       | `\colon `                                            |                                                                                                                                                                                                     |
| `||`       | `\mid`                                               |                                                                                                                                                                                                     |
| `<>`       | `\diamond `                                          |                                                                                                                                                                                                     |
| `dd`       | `\mathrm{d}`                                         |                                                                                                                                                                                                     |
| `par`      | `\partial`                                           |                                                                                                                                                                                                     |
| `,,`       | `\, `                                                |                                                                                                                                                                                                     |
| `inv`      | `^{-1}`                                              |                                                                                                                                                                                                     |
| `com`      | `^c`                                                 |                                                                                                                                                                                                     |
| `cc`       | `\subset `                                           |                                                                                                                                                                                                     |
| `ceq`      | `\subseteq `                                         |                                                                                                                                                                                                     |
| `Nn`       | `\cap `                                              |                                                                                                                                                                                                     |
| `UU`       | `\cup `                                              |                                                                                                                                                                                                     |
| `nNn`      | `\bigcap`                                            | `nnn` would conflict with the snippet `nn` which automatically adds the subscript `_n`.                                                                                                             |
| `uuu`      | `\bigcup`                                            |                                                                                                                                                                                                     |
| `VV`       | `\vee `                                              |                                                                                                                                                                                                     |
| `WW`       | `\wedge `                                            |                                                                                                                                                                                                     |
| `vvv`      | `\bigvee`                                            |                                                                                                                                                                                                     |
| `www`      | `\bigwedge`                                          |                                                                                                                                                                                                     |
| `OO`       | `\emptyset`                                          |                                                                                                                                                                                                     |
| `smi`      | `\setminus `                                         |                                                                                                                                                                                                     |
| `<!`       | `\triangleleft `                                     |                                                                                                                                                                                                     |
| `ell`      | `\ell`                                               |                                                                                                                                                                                                     |
| `quad`     | `\quad `                                             |                                                                                                                                                                                                     |
| `sum`      | `\sum`                                               |                                                                                                                                                                                                     |
| `prod`     | `\prod`                                              |                                                                                                                                                                                                     |
| `lim`      | `\lim`                                               |                                                                                                                                                                                                     |
| `linf`     | `\liminf`                                            | Can't use `liminf` because it would have the existinig snippet `lim` as a prefix.                                                                                                                   |
| `lsup`     | `\limsup`                                            |                                                                                                                                                                                                     |
| `int`      | `\int`                                               |                                                                                                                                                                                                     |
| `oint`     | `\oint`                                              |                                                                                                                                                                                                     |
| `text`     | `\text{}`                                            |                                                                                                                                                                                                     |
| `bb`       | `\mathbb{}`                                          |                                                                                                                                                                                                     |
| `cal`      | `\mathcal{}`                                         |                                                                                                                                                                                                     |
| `scr`      | `\mathscr{}`                                         |                                                                                                                                                                                                     |
| `fra`      | `\mathfrak{}`                                        |                                                                                                                                                                                                     |
| `lr(`      | `\Bigl(\Bigr)`                                       | `\Bigl` and `\Bigr` can of course be replaced by `\left` and `\right`. The closing `)` should be inserted by smartparens, otherwise add it to the snippet.                                          |
| `lr[`      | `\Bigl[\Bigr]`                                       |                                                                                                                                                                                                     |
| `lr|`      | `\Bigl|\Bigr|`                                       |                                                                                                                                                                                                     |
| `set`      | `\{\}`                                               |                                                                                                                                                                                                     |
| `lrset`    | `\Bigl\{\Bigr\}`                                     |                                                                                                                                                                                                     |
| `lan`      | `\langle\rangle`                                     |                                                                                                                                                                                                     |
| `lrlan`    | `\Bigl\langle\Bigr\rangle`                           |                                                                                                                                                                                                     |
| `floor`    | `\lfloor\rfloor`                                     |                                                                                                                                                                                                     |
| `ceil`     | `\lceil\rceil`                                       |                                                                                                                                                                                                     |
| `lrfloor`  | `\Bigl\lfloor\Bigr\rfloor`                           |                                                                                                                                                                                                     |
| `lrceil`   | `\Bigl\lceil\Bigr\rceil`                             |                                                                                                                                                                                                     |
| `norm`     | `\|\|`                                               |                                                                                                                                                                                                     |
| `lrnor`    | `\Bigl\| \Bigr\|`                                    |                                                                                                                                                                                                     |
| `sq`       | `\sqrt{}`                                            |                                                                                                                                                                                                     |
| `td`       | `^{}`                                                |                                                                                                                                                                                                     |
| `rd`       | `^{()}`                                              |                                                                                                                                                                                                     |
| `op`       | `\operatorname{}`                                    |                                                                                                                                                                                                     |
| `case`     | `\begin{cases}`<br/>`\end{cases}`                    |                                                                                                                                                                                                     |
| `pmat`     | `\begin{pmatrix}`<br/>`\end{pmatrix}`                |                                                                                                                                                                                                     |
| `bmat`     | `\begin{bmatrix}`<br/>`\end{bmatrix}`                |                                                                                                                                                                                                     |
| `vmat`     | `\begin{vmatrix}`<br/>`\end{vmatrix}`                |                                                                                                                                                                                                     |
| `binom`    | `\binom{}{}`                                         |                                                                                                                                                                                                     |
| `dint`     | `\int_{}^{}`                                         |                                                                                                                                                                                                     |
| `,iint`    | `\iint_{}`                                           |                                                                                                                                                                                                     |
| `underset` | `\underset{}{}`                                      |                                                                                                                                                                                                     |
| `overset`  | `\overset{}{}`                                       |                                                                                                                                                                                                     |
| `underbr`  | `\underbrace{}_{}`                                   |                                                                                                                                                                                                     |
| `overbr`   | `\overbrace{}^{}`                                    |                                                                                                                                                                                                     |
| `sin`      | `\sin`                                               | To write `s \in` and suppress this snippet, type `s inn`, inserting a space after the `s`.                                                                                                          |
| `cos`      | `\cos`                                               |                                                                                                                                                                                                     |
| `tan`      | `\tan`                                               |                                                                                                                                                                                                     |
| `sec`      | `\sec`                                               |                                                                                                                                                                                                     |
| `csc`      | `\csc`                                               |                                                                                                                                                                                                     |
| `cot`      | `\cot`                                               |                                                                                                                                                                                                     |
| `asin`     | `\arcsin`                                            |                                                                                                                                                                                                     |
| `acos`     | `\arccos`                                            |                                                                                                                                                                                                     |
| `atan`     | `\arctan`                                            |                                                                                                                                                                                                     |
| `asec`     | `\arcsec`                                            |                                                                                                                                                                                                     |
| `acsc`     | `\arccsc`                                            |                                                                                                                                                                                                     |
| `acot`     | `\arccot`                                            |                                                                                                                                                                                                     |
| `arsin`    | `\arsin`                                             | Via this snippet, `arsinh` is replaced by `\arsinh` etc.                                                                                                                                            |
| `arcos`    | `\arcos`                                             |                                                                                                                                                                                                     |
| `artan`    | `\artan`                                             |                                                                                                                                                                                                     |
| `arsec`    | `\arsec`                                             |                                                                                                                                                                                                     |
| `arcsc`    | `\arcsc`                                             |                                                                                                                                                                                                     |
| `arcot`    | `\arcot`                                             |                                                                                                                                                                                                     |
| `min`      | `\min`                                               |                                                                                                                                                                                                     |
| `max`      | `\max`                                               |                                                                                                                                                                                                     |
| `sup`      | `\sup`                                               |                                                                                                                                                                                                     |
| `inf`      | `\inf`                                               |                                                                                                                                                                                                     |
| `grad`     | `\grad`                                              |                                                                                                                                                                                                     |
| `log`      | `\log`                                               |                                                                                                                                                                                                     |
| `exp`      | `\exp`                                               |                                                                                                                                                                                                     |
| `ln`       | `\ln`                                                |                                                                                                                                                                                                     |
| `star`     | `\star`                                              |                                                                                                                                                                                                     |
| `perp`     | `\perp`                                              |                                                                                                                                                                                                     |
| `pll`      | `\parallel`                                          |                                                                                                                                                                                                     |
| `circ`     | `\circ`                                              |                                                                                                                                                                                                     |
| `ker`      | `\ker`                                               |                                                                                                                                                                                                     |
| `coker`    | `\coker`                                             | You might want to replace this by `coer` if `coker` interferes with evil-escape-key-sequence. Or use key-chord to remap escape to `jk` or `kj`. The same is true for `.k`, `..k` and `check` below. |
| `img`      | `\im`                                                |                                                                                                                                                                                                     |
| `coim`     | `\coim`                                              |                                                                                                                                                                                                     |
| `dim`      | `\dim`                                               |                                                                                                                                                                                                     |
| `codim`    | `\codim`                                             |                                                                                                                                                                                                     |
| `span`     | `\operatorname{span}`                                |                                                                                                                                                                                                     |
| `dist`     | `\dist`                                              |                                                                                                                                                                                                     |
| `det`      | `\det`                                               |                                                                                                                                                                                                     |
| `deg`      | `\deg`                                               |                                                                                                                                                                                                     |
| `gcd`      | `\gcd`                                               |                                                                                                                                                                                                     |
| `lc`       | `\lc`                                                | You can also write `lcm`, which becomes `\lcm`.                                                                                                                                                     |
| `sgn`      | `\sgn`                                               |                                                                                                                                                                                                     |
| `nabla`    | `\nabla`                                             |                                                                                                                                                                                                     |
| `grad`     | `\grad`                                              |                                                                                                                                                                                                     |
| `div`      | `\divg`                                              | (custom command)                                                                                                                                                                                    |
| `curl`     | `\curl`                                              | (custom command)                                                                                                                                                                                    |
| `rot`      | `\rot`                                               | (custom command)                                                                                                                                                                                    |
| `Re`       | `\Re`                                                | (custom command)                                                                                                                                                                                    |
| `Im`       | `\Im`                                                | (custom command)                                                                                                                                                                                    |
| `NN`       | `\mathbb{N}`                                         |                                                                                                                                                                                                     |
| `ZZ`       | `\mathbb{Z}`                                         |                                                                                                                                                                                                     |
| `QQ`       | `\mathbb{Q}`                                         |                                                                                                                                                                                                     |
| `RR`       | `\mathbb{R}`                                         |                                                                                                                                                                                                     |
| `CC`       | `\mathbb{C}`                                         |                                                                                                                                                                                                     |
| `TT`       | `\mathbb{T}`                                         |                                                                                                                                                                                                     |
| `DD`       | `\mathbb{D}`                                         |                                                                                                                                                                                                     |
| `FF`       | `\mathbb{F}`                                         |                                                                                                                                                                                                     |
| `KK`       | `\mathbb{K}`                                         |                                                                                                                                                                                                     |
| `PP`       | `\mathbb{P}`                                         |                                                                                                                                                                                                     |
| `XX`       | `\mathbb{E}`                                         |                                                                                                                                                                                                     |
| `.a`       | `\alpha`                                             |                                                                                                                                                                                                     |
| `.b`       | `\beta`                                              |                                                                                                                                                                                                     |
| `.g`       | `\gamma`                                             |                                                                                                                                                                                                     |
| `.d`       | `\delta`                                             |                                                                                                                                                                                                     |
| `.e`       | `\epsilon`                                           |                                                                                                                                                                                                     |
| `..e`      | `\varepsilon`                                        |                                                                                                                                                                                                     |
| `.z`       | `\zeta`                                              |                                                                                                                                                                                                     |
| `.h`       | `\eta`                                               |                                                                                                                                                                                                     |
| `.q`       | `\theta`                                             |                                                                                                                                                                                                     |
| `..q`      | `\vartheta`                                          |                                                                                                                                                                                                     |
| `.i`       | `\iota`                                              |                                                                                                                                                                                                     |
| `.k`       | `\kappa`                                             |                                                                                                                                                                                                     |
| `..k`      | `\varkappa`                                          |                                                                                                                                                                                                     |
| `.j`       | `\kappa`                                             |                                                                                                                                                                                                     |
| `..j`      | `\varkappa`                                          |                                                                                                                                                                                                     |
| `.l`       | `\lambda`                                            |                                                                                                                                                                                                     |
| `.m`       | `\mu`                                                |                                                                                                                                                                                                     |
| `.n`       | `\nu`                                                |                                                                                                                                                                                                     |
| `.x`       | `\xi`                                                |                                                                                                                                                                                                     |
| `.p`       | `\pi`                                                |                                                                                                                                                                                                     |
| `..p`      | `\varpi`                                             |                                                                                                                                                                                                     |
| `.r`       | `\rho`                                               |                                                                                                                                                                                                     |
| `..r`      | `\varrho`                                            |                                                                                                                                                                                                     |
| `.s`       | `\sigma`                                             |                                                                                                                                                                                                     |
| `..s`      | `\varsigma`                                          |                                                                                                                                                                                                     |
| `.t`       | `\tau`                                               |                                                                                                                                                                                                     |
| `.u`       | `\upsilon`                                           |                                                                                                                                                                                                     |
| `.f`       | `\phi`                                               |                                                                                                                                                                                                     |
| `..f`      | `\varphi`                                            |                                                                                                                                                                                                     |
| `.c`       | `\chi`                                               |                                                                                                                                                                                                     |
| `.y`       | `\psi`                                               |                                                                                                                                                                                                     |
| `.o`       | `\omega`                                             |                                                                                                                                                                                                     |
| `.w`       | `\omega`                                             |                                                                                                                                                                                                     |
| `.A`       | `\aleph`                                             |                                                                                                                                                                                                     |
| `.G`       | `\Gamma`                                             |                                                                                                                                                                                                     |
| `.D`       | `\Delta`                                             |                                                                                                                                                                                                     |
| `.Q`       | `\Theta`                                             |                                                                                                                                                                                                     |
| `.L`       | `\Lambda`                                            |                                                                                                                                                                                                     |
| `.X`       | `\Xi`                                                |                                                                                                                                                                                                     |
| `.P`       | `\Pi`                                                |                                                                                                                                                                                                     |
| `.S`       | `\Sigma`                                             |                                                                                                                                                                                                     |
| `.U`       | `\Upsilon`                                           |                                                                                                                                                                                                     |
| `.F`       | `\Phi`                                               |                                                                                                                                                                                                     |
| `.Y`       | `\Psi`                                               |                                                                                                                                                                                                     |
| `.O`       | `\Omega`                                             |                                                                                                                                                                                                     |
| `.W`       | `\Omega`                                             |                                                                                                                                                                                                     |
