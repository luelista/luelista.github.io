---
date: 2025-03-23
description: PRE Workbench maintenance update: now available for NixOS; fixed bugs with Floating Dock Windows on Linux; fixed occasional error message while scrolling in HexView
---

# PRE Workbench maintenance update

After some time of hibernation, I'll published a new release of **Protocol Reverse Engineering Workbench**. 

* Updated Qt-Advanced-Dock-Layout to version 4.3.1, this should fix the problems where Floating (non-docked) sub-windows couldn't be re-docked again.
* Fixed occasional error messages during scrolling in HexView
* Provide *flake* for NixOS, you can run it with `nix run github:luelista/pre_workbench`.

For more information on PRE Workbench, refer to [the website](https://luelista.net/pre_workbench/).
