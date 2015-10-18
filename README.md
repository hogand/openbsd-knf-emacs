# OpenBSD KNF for Emacs #

[OpenBSD](http://www.openbsd.org) uses Kernel Normal Form (KNF) for formatting C
code.  For details, see
[style(9)](http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man9/style.9).

While KNF is a simple set of rules, it can be difficult to find editor support.
Some of the rules go against common conventions used by editors.  The biggest
obstacle is our 4 space continuation rule.  A lot of other style guides will
indent based on the context such as indenting function arguments.  The OpenBSD
style guide is rigid and always uses tabs for first level indenting and tabs
plus 4 space indents for the second level indenting.

While this tries to be compliant with KNF, it is not perfect.  It's good enough
that I have been using it for over a year with OpenBSD code.  If you want to
write a tool that can handle KNF perfectly, adding support to clang-format
would be a better (and more involved) solution.

## Usage ##

There are several ways of incorporating this into your Emacs setup.

This assumes you want to use the `~/.emacs.d` convention and have placed the
style file in `~/.emacs.d/elisp/openbsd-knf-style.el`.

This is a simple `~/.emacs.d/init.el` that enables OpenBSD KNF:

```lisp
;; Emacs 24.5 config
(add-to-list 'load-path "~/.emacs.d/elisp/")

;; OpenBSD KNF for C/C++
(require 'openbsd-knf-style)
(c-add-style "OpenBSD" openbsd-knf-style)
(setq c-default-style '((c-mode . "OpenBSD")))
```

Here's a slightly more realistic `~/.emacs.d/init.el`:

```lisp
;; Emacs 24.5 config
(add-to-list 'load-path "~/.emacs.d/elisp/")

(line-number-mode 1)
(column-number-mode 1)
(auto-compression-mode 1)

(setq search-highlight 1)
(setq query-replace-highlight 1)
(setq show-paren-delay 0
      show-paren-style 'parenthesis)
(setq blink-matching-paren t)
(transient-mark-mode 1)
(show-paren-mode 1)

;; General coding style.
(setq-default show-trailing-whitespace t)
(setq whitespace-style '(trailing lines space-before-tab)
      whitespace-line-column 80)
(global-whitespace-mode 1)
(global-font-lock-mode 1)

;; OpenBSD KNF for C/C++
(require 'openbsd-knf-style)
(c-add-style "OpenBSD" openbsd-knf-style)
(setq c-default-style '((c-mode . "OpenBSD")))

;; In the modeline, show which function you are currently in.
(add-hook 'c-mode-common-hook
	  (lambda ()
	    (when (derived-mode-p 'c-mode 'c++-mode 'java-mode)
	      (which-func-mode 1))))

;; Dired shows all files by default.  Use dired-x to stop displaying junk.
(add-hook 'dired-load-hook
          (lambda ()
            (load "dired-x")
            ;; I like to use '..' to navigate with dired so omit it here.
            (setq dired-omit-files "^\\.?#\\|^\\.$")
          ))
(add-hook 'dired-mode-hook
          (lambda ()
            (dired-omit-mode 1)
          ))
```

## Limitations ##

In practice, this works 95-100% of the time for me when editing code that's
already in KNF format.  It's 80-90% when applying KNF to a file that wasn't
using it before because you're more likely to run into edge cases.

