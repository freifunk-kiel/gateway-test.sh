gateway-test.sh
===============

* This script must be run as root on a mashine that is in Freifunk 
* Before the first start, it will tell you to activate the rp_filter with:

        sysctl -w net.ipv4.conf.$INTERFACE.rp_filter=0

# Usage

    # dependency
    apt-get install -y dhcping
    
    # clone this repository
    git clone https://github.com/freifunk-kiel/gateway-test.sh
    cd gateway-test.sh
    
    # start the bash script as root
    sudo ./gateway-test.sh

# Options

    --help  show this README.md
    -g      gatewaylist, for example sudo gateway-test.sh -g "13.gw/10.187.53.1/fd42:eb49:c0b5:4242::fd13"
    -v      verbose mode

# Configuration

If you want to add your own community:

* define a list of gateways to test in the array `DEFAULT_GATEWAYS`
* define your interface which should be used for pinging a remote host for example `br-freifunk` or `wlan0`
* define a dns record you like to receive at `TARGET_DNS_RECORD` and which should resolve in `TARGET_DNS_FFKI_RECORD`

# Tests
    
* test Gateway reachability
* Gateway functionality ping
* Gateway functionality ping6
* DHCP test
* Nameserver test with `nslookup $TARGET_DNS_RECORD`
* Nameserver test for an own domain of the Community
* Check for duplicate Nameserver SOA Record
* test ping in different sizes and show the maximum package size which can be transmitted

# manual test:

    gw=10.116.152.1
    FWMARK=100
    ROUTING_TABLE=100
    ip rule add fwmark ${FWMARK} table ${ROUTING_TABLE}
    ip route add 0.0.0.0/1 via $gw table ${ROUTING_TABLE}
    ip route add 128.0.0.0/1 via $gw table ${ROUTING_TABLE}
    ip route replace unreachable default table ${ROUTING_TABLE}

    ping -m 100 -I wlp1s0 -c 2  -i .2 -W 2 -q 217.70.197.62
    
    #cleanup:
    ip route flush table ${ROUTING_TABLE}
    ip rule del fwmark ${FWMARK} table ${ROUTING_TABLE}
    
