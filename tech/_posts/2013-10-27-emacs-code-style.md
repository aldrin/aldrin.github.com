---
title: Invoking Code-Style Tools in Emacs
tags: Short
---

If the number of spaces people leave before and after an assignment operator matters to you and if
incorrect placement of braces annoys you, you really need to consider elevating your thoughts to the
realm of more important things. While you do that, here's an elisp function to take care of the code
style.

``` cl
(defun code-style (tool args) 
  "enforce code style"
  (let ((position (point))) 
    (progn 
      (if (executable-find tool) 
          (shell-command-on-region 
           (point-min) (point-max) 
           (format "%s %s" tool args) t t) 
        (message (format "can't find %s"  tool)))) 
    (goto-char position)))
```

The function will invoke your favourite code-styling tool on the current buffer. For C++ I prefer
[clang-format](http://clang.llvm.org/docs/ClangFormat.html), for Python
[autopep8](http://pypi.python.org/pypi/autopep8) and for JavaScript
[jsbeautifier](http://jsbeautifier.org). 

Once these are on your `PATH` they can be hooked into the mode as follows:

``` cl
;; invoker for style tools
(defun clang-format-buffer()
  "clang-format buffer"
  (interactive)
  (code-style "clang-format" "-style=LLVM"))

(defun pep8-buffer()
  "pep-8 style on the buffer"
  (interactive)
  (code-style "autopep8" "-a --max-line-length=99 -"))

(defun jsbeautify-buffer()
  "beautify the js buffer."
  (interactive)
  (code-style "js-beautify" "-i -s2 -j"))

;; hook functions for modes
(defun my-python-settings()
  (local-set-key (kbd "M-i") 'pep8-buffer))
(defun my-c-mode-settings()
  (local-set-key (kbd "M-i") clang-format-buffer))
(defun my-js-settings()
  (local-set-key (kbd "M-i") 'jsbeautify-buffer))

;; register the hooks
(add-hook 'c-mode-common-hook 'my-c-mode-settings)
(add-hook 'python-mode-hook 'my-python-settings)
(add-hook 'js-mode-hook 'my-js-settings)
```

That's about it.
