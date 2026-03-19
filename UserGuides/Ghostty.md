# Ghostty

```
$ screen -r
Cannot find terminfo entry for 'xterm-ghostty'.
$ tmux a
missing or unsuitable terminal: xterm-ghostty
```

We need to export a compatible TERM before,
```
TERM=xterm-256color
```
