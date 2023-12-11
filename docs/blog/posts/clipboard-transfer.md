---
date: 2023-12-10
---

# Simple one-way clipboard sync

I needed a simple way to continuously transfer the clipboard from one computer to another, so I hacked together these two oneliners.
<!-- more -->

```bash title="Sender"
# replace 10.0.0.1 with receiver's IP address
while true; do if [ "$last" != "$(xclip -o)" ]; then last="$(xclip -o )"; xclip -o | nc -vc 10.0.0.1 9999; fi; sleep 1; done
```

```bash title="Receiver"
while true ; do nc -vl 9999 | xclip -i -selection clipboard ; done
```

If one of the machines is on macOS, replace `xclip -o` with `pbpaste` or `xclip -i -selection clipboard` with `pbcopy`.


!!! warning

    Only use this on trusted networks and with trusted machines, the clipboard contents are transmitted unencrypted!


