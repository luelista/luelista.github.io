---
date: 2024-11-17
description: How to temporarily get rid of Gnome's "Oh no! Something has gone wrong." dialog to try and save your precious data hidden underneath it.
---

# Snatch your half-crashed desktop session from gnome-session-failed's claws

If the system[0] thinks that something went horribly wrong in a Gnome desktop session, it starts the systemd user unit [`gnome-session-failed.service`](https://github.com/GNOME/gnome-session/blob/main/data/gnome-session-failed.service.in), which runs the `@libexecdir@/gnome-session-failed` executable with the parameter ` --allow-logout`. This results in the following full-screen message, blocking any other interaction with the session than clicking "Log out", or using ctrl-alt-Fx to switch to a VT:

(insert result of ```sh -c "`nix eval --raw nixpkgs#gnome-session`/libexec/gnome-session-failed --allow-logout &"; sleep 1 ; scrot gnome-session-failed.png ; sleep 1 ; pkill -f gnome-session-failed```)

> *Oh no! Something has gone wrong.* A problem has occured and the system can't recover. Please log out and try again. \[Log out]

The easiest way to deal with this is to just click on "Log out" and try again. 

Let's say however, you have some important, unsaved data you really need to access (or, like me, you hear that your music is still playing, meaning the session can't be *that* broken, and are too stubborn to let Gnome force you to Log out).

In some circumstances, it is possible to super-shift-middleclick on the window to open the window manager context menu, uncheck "Always on top" and "On all workspaces", and just move it to a different workspace.

If that is not possible, we need to get to a shell on that machine somehow, either by switching to a VT using ctrl-alt-Fx, or connect via SSH from another device. One might want to just kill the `gnome-session-failed` process now, however, that would close the full-screen message, but the session would still terminate afterwards. This is due to the `ExecStopPost=-@libexecdir@/gnome-session-ctl --shutdown` line in the service file. To get around this, we temporarily override the service file:

```
systemctl --user edit --runtime gnome-session-failed.service
```

Insert the following:
```
[Unit]
OnFailure=
[Service]
ExecStopPost=
```

Reload the service config:
```
systemctl --user daemon-reload
```

Now you can safely kill the process:
```
pkill -f gnome-session-failed
```

Your session may be severely broken (i.e. window manager crashed, so no window decorations, window switching), but you might be able to save your file (or start another window manager like i3 in your session and write a blog post about it).

----

[0] I haven't figured out yet which part of the system is responsible here
