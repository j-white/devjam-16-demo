# Underling Provisioning & SNMP Data Collection Demo

## Agenda

1. Solution Overview
1. OpenNMS Setup
1. Minion Setup
1. Karaf Shell Extensions
1. Provisioning

## Solution Overview

### Underling

Minion written in Go, see https://github.com/j-white/underling.

### Diagram

![Lab Setup](diag.png "Lab Setup")

### Hardware

2 x [Orange Pi One](http://www.orangepi.org/orangepione/)

* 1.2 Ghz ARM Cortex
* 512MB DDR
* 10$
* Running ARMBIAN Debian GNU/Linux 8 (jessie) 3.4.112-sun8i

### Network setup

noise is @ 192.168.1.1

ob1 is @ 192.168.1.100 (86:54:e1:31:66:76)

ob2 is @ 192.168.1.101 (9a:0d:90:08:93:90)

    sudo ifconfig enp0s25 192.168.1.1/24
    sudo systemctl restart dhcpd


### OpenNMS Setup

Underling uses the STOMP protocol for communicating with OpenNMS's ActiveMQ service.

To enable STOMP, add the following to `etc/opennms-activemq.xml` in the `<transportConnectors>` section:

```xml
<transportConnector name="stomp" uri="stomp+nio://0.0.0.0:61613"/>
```

### Setting up ob2

Install Go

    sudo apt-get install golang

Setup your GOPATH

    export GOPATH=/home/jesse/go

Setup godep

    go get github.com/tools/godep
    export PATH=$PATH:$GOPATH/bin

Grab the code

    go get github.com/j-white/underling

Build

    cd $GOPATH/src/github.com/j-white/underling
    godep go build

Running

    cp underling.yaml.sample underling.yaml
    vi underling.yaml (update location to DEMO and url to aws-nms-1)
    screen
    ./underling

### Karaf Shell Extensions

    snmp:walk -l DEMO 192.168.1.100 .1.3.6.1.4
    snmp:walk -l DEMO 192.168.1.101 .1.3.6.1.4
    provision:detect -l DEMO ICMP 192.168.1.100
    provision:detect -l DEMO WS-Man 192.168.1.100

### Provisioning

    http://aws-nms-1:8980/opennms
