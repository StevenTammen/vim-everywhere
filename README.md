## General Idea

Handling Vim modes on the keyboard/remapping-script level has several advantages to tying implementation to a specific program (that keeps track of modes).

Autospaced layouts will only want to send raw characters when in normal mode (and visual mode, etc.). This can be handled by having the Vim-entry key enter a state where only raw characters are sent.

One way to get out of this state when entering insert mode, if using a program like Emacs, is to run a command through a hook. For example:

```
;; Use hook to re-enable insert-mode autospacing in combination with remapping script 

 (defun reset-autospacing ()
  "Runs ahk file to reset autospacing."
  (interactive)
  (call-process-shell-command "C:\\Users\\Steven\\Dropbox\\Keyboards\\remap\\resetAutospacing.ahk" nil 0))

(add-hook 'evil-insert-state-entry-hook 'reset-autospacing)
```

This is suboptimal, however, since it introduces lag. (It may be possible to call ~async-shell-command~ with ~call-process~, but I didn't see an obvious way on first glance). It is possible, if you type fast enough, for some characters to be entered between when insert mode is entered in Emacs and when the autospacing state gets reset in a remapping script. This is no good, and can cause unwanted behavior.

The solution to all this is to keep track of Vim states on the keyboard (or at least remapping-script) level. This lets autospacing changes take effect immediately. A very useful side-benefit of tracking things like this is that it lets custom keybindings get generated when in the virtual normal mode, visual mode, etc.

This would theoretically allow binding keys to functions to emulate Vim behavior across all applications. For example ~$~ could get bound to ~End~ while still getting used normally in normal text insertion.

