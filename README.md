# consult.el - Consulting completing-read

This package provides various commands based on the Emacs completion function
`completing-read`, in particular a more advanced buffer switching command and a
variant of [Swiper](https://github.com/abo-abo/swiper#swiper). The commands are
compatible with completion-systems based on the standard Emacs API, e.g., the
Emacs builtin
[Icomplete](https://www.gnu.org/software/emacs/manual/html_node/emacs/Icomplete.html)
and [Selectrum](https://github.com/raxod502/selectrum). If Icomplete is used, it
is recommended to install
[Icomplete-vertical](https://github.com/oantolin/icomplete-vertical). The goal
is to keep the completion-system specifics in this package to a minimum.

Note that if you use [Ivy](https://github.com/abo-abo/swiper#ivy) or
[Helm](https://github.com/emacs-helm/helm), you don't need Consult, since both
packages already bring their own rich set of additional commands.

## Screenshots

consult-annotate-mode

![consult-annotate-mode](https://github.com/minad/consult/blob/master/images/consult-annotate-mode.png?raw=true|height)

consult-mark

![consult-mark](https://github.com/minad/consult/blob/master/images/consult-mark.png?raw=true|height)

consult-line

![consult-line](https://github.com/minad/consult/blob/master/images/consult-line.png?raw=true)

consult-outline

![consult-outline](https://github.com/minad/consult/blob/master/images/consult-outline.png?raw=true)

## Available commands

Most provided commands follow the naming scheme `consult-thing`. Furthermore
there is `consult-annotate-mode` which enriches the completion display with
documentation strings.

### Virtual Buffers

  * `consult-buffer` (`-other-window`, `-other-frame`): Enhanced version of
     `switch-to-buffer` with support for virtual buffers. Supports live preview
     and recursive editing while previewing. If Selectrum is used
     `consult-buffer` supports prefixes for narrowing. You can type `b SPC`, `f
     SPC`, `m SPC` and `v SPC` in order to narrow to buffers, files, bookmarks
     and views respectively. Unfortunately this is (not yet?) supported by the
     generic `completing-read` implementation.
  * `consult-bookmark`: Select or create bookmark. You might prefer the more
    powerful `consult-buffer` instead, which includes bookmarks.
  * `consult-recent-file` (`-other-window`, `-other-frame`): Select a recent
     files. You might prefer the more powerful `consult-buffer` instead, which
     includes recent files.

### Editing

  * `consult-register`: Select from list of registers.
  * `consult-yank`, `consult-yank-pop`: Enhanced version of `yank` and
    `yank-pop` which allows selecting from the kill-ring.

### Help/Discoverability

  * `consult-apropos`: Replacement for `apropos` with completion.
  * `consult-annotate-mode`: Enable this mode to annotates completions with
    richer information (e.g. `M-x`, `describe-face`, `describe-symbol`,
    `helpful-function`, etc).

### Histories

  * `consult-command-history`: Select a command from the `command-history`.
  * `consult-minibuffer-history`: Insert a string from the `minibuffer-history`.

### Jumping and Search

  * `consult-line` (`-symbol-at-point`, `-from-isearch`): Select from matching
    lines. Supports live preview and recursive editing of the preview. There are
    two variants, which search for the symbol at point and for the most
    recent isearch string respectively.
  * `consult-mark`: Jump to a marker in the `mark-ring`. Supports live preview
    and recursive editing of the preview.
  * `consult-outline`: Jump to a heading of the outline. Supports live preview
    and recursive editing of the preview.
  * `consult-multi-occur`: Replacement for `multi-occur`.

### Miscellaneous

  * `consult-minor-mode`: Enable/disable minor mode.
  * `consult-theme`: Select a theme and disable all currently enabled themes.
    Supports live preview of the theme while scrolling through the candidates.

## Live previews

Some of the commands support live previews. For example when you scroll through
the items of `consult-line`, the buffer will jump to the corresponding position.
It is possible to jump back and forth between the minibuffer and the buffer to
perform recursive editing while a search is ongoing. In case you do not like
live previews or find them distracting, for each of the commands supporting
preview, there is a customizable variable which allows disabling the preview. In
order for live previews to work you must enable `consult-preview-mode`.

## Package configuration

It is recommended to manage package configurations with `use-package`. The
Consult package only provides commands and does not add any keybindings. In
order to use the enhanced commands, you must configure the keybindings yourself.

~~~ elisp
;; Example configuration
(use-package consult
  ;; Replace bindings. Lazily loaded due to use-package.
  :bind (("C-c o" . consult-outline)
         ("C-x b" . consult-buffer)
         ("C-x 4 b" . consult-buffer-other-window)
         ("C-x 5 b" . consult-buffer-other-frame)
         ("C-x r x" . consult-register)
         ("C-x r b" . consult-bookmark)
         ("M-g o" . consult-outline) ;; "M-s o" is a good alternative
         ("M-g m" . consult-mark)    ;; "M-s m" is a good alternative
         ("M-g l" . consult-line)    ;; "M-s l" is a good alternative
         ("M-s m" . consult-multi-occur)
         ("M-y" . consult-yank-pop)
         ("<help> a" . consult-apropos))

  ;; The :init configuration is always executed (Not lazy!)
  :init

  ;; Replace functions (consult-multi-occur is a drop-in replacement)
  (fset 'multi-occur #'consult-multi-occur)

  ;; Optionally enable previews. Note that individual previews can be disabled
  ;; via customization variables.
  (consult-preview-mode)

  ;; Optionally enable richer annotations during completion.
  ;; Must be in the :init section of use-package such that the mode gets
  ;; enabled right away. Note that this forces loading the package.
  (consult-annotate-mode)

  ;; Configure other variables in the :config section, after lazily loading the package
  :config

  ;; Enable richer annotations for M-x.
  ;; Only keybindings are shown by default, in order to reduce noise for this very common command.
  ;; * consult-annotate-symbol: Annotate with the documentation string
  ;; * consult-annotate-command-binding (default): Annotate only with the keybinding
  ;; * consult-annotate-command-full: Annotate with the keybinding and the documentation string
  ;; (setf (alist-get 'execute-extended-command consult-annotate-alist) #'consult-annotate-command-full)

  ;; Enable richer annotations for other functions.
  ;; (push '(my-fancy-command . consult-annotate-symbol) consult-annotate-alist)
  )
~~~

### Configuration settings

| Variable                   | Def | Description                                             |
|----------------------------|-----|---------------------------------------------------------|
| consult-annotate-alist     | …   | Functions which should get a richer completion display. |
| consult-line-numbers-widen | t   | Show absolute line numbers when narrowing is active.    |
| consult-preview-buffer     | t   | Enable buffer preview during selection                  |
| consult-preview-theme      | t   | Enable theme preview during selection                   |
| consult-preview-mark       | t   | Enable mark preview during selection                    |
| consult-preview-line       | t   | Enable line preview during selection                    |
| consult-preview-outline    | t   | Enable outline preview during selection                 |
| consult-themes             | nil | List of themes to be presented for selection            |

## Acknowledgments

You probably guessed from the name that this package is inspired by and
partially derived from [Counsel](https://github.com/abo-abo/swiper#counsel)
(Author Oleh Krehel, Copyright Free Software Foundation, Inc.). Note that we are
far from counsel in terms of covered functionality. Furthermore some of the
commands found in this package were taken from the [Selectrum
wiki](https://github.com/raxod502/selectrum/wiki/Useful-Commands).
