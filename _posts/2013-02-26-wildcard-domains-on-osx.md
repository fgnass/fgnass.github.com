---
title: Local Wildcard Domains on OS X
layout: post
---

This post shows how to map `*.dev` domains to localhost and make them accessible
from within VirtualBox.

## Adding an IP alias

By default VirtualBox maps the IP `10.0.2.2` to the host's loopback interface.
So instead of mapping `*.dev`domains to `127.0.0.1` as one would normally do we
let them resolve to 10.0.2.2 and set up an IP-Alias for the loopback
interface. In order to permanently add the alias we have to put this [plist](https://gist.github.com/fgnass/5037138/raw/676369616ee8ba5f8c77a9b32d2a60ecd05edaf0/ipalias.plist)
file into the `/Library/LaunchDaemons/`:

    # Download the gist
    sudo curl https://gist.github.com/fgnass/5037138/raw/676369616ee8ba5f8c77a9b32d2a60ecd05edaf0/ipalias.plist -o /Library/LaunchDaemons/ipalias.plist

    # Let launchd execute the command
    sudo launchctl load /Library/LaunchDaemons/ipalias.plist

To verify that the command was executed successfully run
`sudo ifconfig lo0 | grep 10.0.2.2` which should display the following output:

    inet 10.0.2.2 netmask 0xff000000

## Installing a DNS forwarder

To actually resolve the `*.dev` domains we install
[dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) a lightweight
DNS forwarder:

    # Install dnsmasq via homebrew
    brew install dnsmasq

    # Resolve *.dev domains to 10.0.2.2
    echo "address=/.dev/10.0.2.2" > `brew --prefix`/etc/dnsmasq.conf

    # Launch on Startup
    sudo cp `brew --prefix dnsmasq`/homebrew.mxcl.dnsmasq.plist /Library/LaunchDaemons
    sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist

    # Use dnsmasq as nameserver
    sudo bash -c 'mkdir /etc/resolver && echo "nameserver 127.0.0.1" > /etc/resolver/dev'

You should now be able to ping `foo.dev` as well as any other `.dev` domain.

## Setting up the nameserver in your Virtual Machines

The last step is to tell your VMs to use `10.0.2.2` as DNS-Server.
Under Windows this looks like this:

![Screenshot](/images/dns.jpg)


