---
date: 2025-04-15
tags:
- enshittification
---

# Remove ads for Mozilla services from Firefox

For some reason, Mozilla feels it's a good idea to litter Firefox with ads for their own value-added services. At least most of them can be disabled, usually by some `about:config` options.

* Hide the "Try other protection tools from Mozilla" area in Firefox Account menu  
  `identity.fxaccounts.toolbar.pxiToolbarEnabled = true`

* Remove Pocket  
  `extensions.pocket.enabled = true`

* Remove Sponsored Links on New Tab Page  
  `browser.newtabpage.activity-stream.showSponsored	= false`  
  `browser.newtabpage.activity-stream.showSponsoredTopSites = false`

  (alternative: open new tab -> click settings icon in bottom right -> uncheck "Sponsored shortcuts"

(to be continued -- enshittification intensifies 🙄)

References:

- https://mozilla.github.io/policy-templates/
- https://www.quippd.com/firefox/wiki/useful-customizations/

