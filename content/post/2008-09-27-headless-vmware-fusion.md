---
comments: true
created_at: 2008/09/27}
date: 2008-09-27T00:00:00Z
title: Headless VMWare Fusion
url: /2008/09/27/headless-vmware-fusion/
---

The latest version of [VMWare Fusion](http://www.vmware.com/products/fusion/) lets you run virtual machines in headless mode. Which is pretty handy if you're using a Linux VM to mirror your live environment. The strange thing is that it's not enabled by default. To enable it you need to run the following on your console: <code>defaults write com.vmware.fusion fluxCapacitor -bool YES</code>