# Getting Started
* Promiscuous Mode: If the NIC discards the packets, then your OS cannot pass
Those packets up to the Wireshark layer. To allow Wireshark to receive packets
that are not destined for your computer, we need to put the NIC into promiscuous mode

![image](https://user-images.githubusercontent.com/31813625/33509184-6cd77a90-d6cd-11e7-902c-730daeb20fcf.png)

* WinPcap: The Windows driver that captures packets and parses them up to
Wireshark
* USBPcap: A sniffer which sniffs the USB ports on your computer (After installation, reboot is needed)
* Wireshark is not an IPS. With Wireshark we capture packets when needed
* Wireshark supports over 850 protocols (and it is growing) such as
HTTP, HTTPS, DHCP, DNS, ICMP, RIP, OSPF, BGP, IPsec prorocols, AppleTalk,
Mobile Telephony, SIGTRAN, Sensor IoT (ZigBee, Wireless heart, Bluetooth low energy) ...
* Protocol Dissector: Dissects a specific protocol and labels the individual
fields for display in Wireshark
  * If there is a protocol that is not supported by Wireshark, you can
  develop your own dissector to look at that protocol

* Wireshark Window is broken into three panes.
  * Packet list pane (One line for every packet)
  * For each packet underneath we have packet details pane
  * packet bytes pane: The actual content of the packet

![image](https://user-images.githubusercontent.com/31813625/33509882-9034a41c-d6d3-11e7-9a68-29fa3f1105d3.png)

* Wireshark does not exposed to physical layer (exception with Wi-Fi networks)

* Practiced Mark, Save, and Export the packets

### Best Practice and Action Plan
* Dont capture too much data
  * Bandwidth issue
  * Storage issue
* Create a network map
  * Physical inventory
  * Digital inventory
  * Identify network node addresses
* Avoid impacting live network traffic
  * Port mirroring on the switch
  * Full duplex tap (does not scale well)
* Avoid getting  sidetracked (don't forget your initial goal)
* Maintain a healthy skepticism: Ask yourself, is wireshark displaying
this information correctly?
  * Byte pane displays the bits that are captured by NIC
  * Then the Wireshark applicaion interprets those 1s and 0s and displays
  in Packet Details pane
  * You can use specifications for the individual fields and their purpose for that specific protocol
  We can get the specification by searching in Google like `ietf ip specification`
  and then in RFC document you can search for Header Format


### Two types of Filters

#### Applied while capturing the traffic (Capture Filters)
* What capture you capture and what you don't
* Limits size of the capture
* Improves performance
* Focus on desired traffic
* If you are not sure what you are looking for, avoid capture filtering
* Syntax is different from Display Filter
  * Type qualifier:
    * Host name
    * Network Address
    * Port numbers
  * Direction qualifier:
    * Source (src)
    * Destination (dst)
    * Source and Destination (src and dst )
  *  Protocol qualifier:
    * ARP
    * Ether
    * IP or IPv6
    * TCP
    * UDP
    * WLAN
  * General Suntax Format: Protocol Direction Type
    * We can have AND OR NOT
  * Example:
    * `arp`
    * `no arp`
    * `tcp port 8080`
    * `ether proto 0x888E` for 802.1x (https://www.iana.org/assignments/ieee-802-numbers/ieee-802-numbers.xhtml)
    * `host 192.168.0.1`
    * `not port 80 and not port 25 and host www.wireshark.org`

##### Ring Buffers:
* Ring buffer is simply a suite of files
* Useful when troubleshooting intermittent problems

###### Configuring a ring buffer
1. Open up Wireshark - Capture Interfaces dialog box
2. Input tab: Select the capturing interface
3. Output: Checkbox `Use a ring buffer with # files`

##### Name Resolution
* Is not recommended to be enabled
* You can enable or disable name resolution for the following cases in Wireshark - Preferences menu
  * MAC address (checks the MACs and resolve's the vendors')
  * Transport names (checks the port and returns the name of the protocol)
  * IP address (returns the hostname)
* Wireshark doesn't inject any traffic into the network with exception of
this name resolution

#### Applied after capturing the traffic (Display Filters)
* Find specific packets
* Find specific events
* Find specific protocol attributes
* Keeps all packets
* No data loss
* Syntax is more complicated than capture filters
* Syntax:
  * Protocols/Protocol_fields String Relation Value
* Relation:
  * eq ==
  * ne !=
  * gt >
  * lt <
  * ge >=
  * le =<
  * Is present
  * Contains
  * Matches
* Examples:
  * `udp`
  * `udp.port == 443`
  * `!(udp.port == 443) and udp`
  * `ip dst == 10.0.0.1`
  * `ip dst eq 10.0.0.1`
  * `tcpc.port == 80 and ip.addr == 10.10.10.1`
  * `eth.type == 0x0806` (ARP)
  * `ip.proto == 17` UDP

* Worked on Adding and Deleting a Display Filter to the Toolbar
* Worked Different Ways to Create a Display Filter: Right Click on the field we want to filter by (in Packet Details pane)

### Coloring Rules
* Color packet with specific properties
  * EAPoL
  * TCP retransmisison
  * Failed password errors
* The Syntax is the same as Display Filter
* View -> Coloring Rules...
  * Add a new rule for `tcp.analysis.duplicate_ack or tcp.analysis.retransmission`
  * Coloring rules are in order (acting as ACL. Put more details first and more general last. You can move rules by drag and drop method)

#### Coloring a conversation:
* Flow of packets between two endpoints.
  * TCP
  * UDP
  * IP
  * Ethernet
* Temporary:
  * Right click in Packet List/Details pane
  * Or select ctrl and a number key
* Limited to 10 colors
Do:
  1. Statistics -> Endpoints
  2. Right click on the packet on the Packet List pane and then select Colorize Conversation
  3. ctrl + space bar ro remove the colorin rules

### Creating Configuration Profiles in Wireshark
* To do repetitive and common tasks
  * Personal Preferences
  * Capture Filters
  * Display Filters
  * Coloring Rules
* Saves your time
* Improve your efficiency
* Edit -> Configuration Profiles...
* How to share a Configuration Profiles?
  * They are stored as a text file
  * Help -> About Wireshark -> Select "Folders" tab -> Click "Personal configuration" location
* Never change your "default profile"
  * If you did and now you need to reset your default profile: got to its configuration directory and delete files (not folders) except "language" and "recent_common"
* Every time you open Wiresharl look at the status bar and the current configuration profile

#### Use case: Analyze Network Performance
* Preferences
  * Turn off checksum validation
  * Add columns: RTT, WinSize
* Capture filters
  * Remove ARP frames
* Display filters expressions
  * TCP errors
  * DNS errors
* Coloring rules
  * Bad TCP
  * HTTP errors

* I will populate my own configuration profile in this repo to have it
accessible online
