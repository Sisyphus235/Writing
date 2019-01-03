

# Chapter 1. Fixing physical networks
* CAT-5 cables
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20181229163914.png)
Orange and green wires send and receive data.
If a wire is striped, then this means that it is positive. If a wire is solid, then the wire is negative.
Blue and brown wires are reserved for future bandwidth capacity.

* coaxial cable
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20181229172636.png)
It uses one big copper wire rather than four twisted pairs.

* fiber-optic cable
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20181229182853.png)

* bandwidth
how much data can flow through the wires on a cable, it's a capacity. Megabits per second (Mbps) is a bandwidth rate.

* network speed
the rate that data can move on a wire, it's a rate. Megabytes per second (MBps) is a data transfer rate used in computing.

# Chapter 2. Planning network layouts
* cable management hardware
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102103939.png)

# Chapter 3. Tools and troubleshooting
* potential network cable problems
broken cables; bad connectors; mis-wired connectors; too close to a power cable; too long a cbale; wrong type of cable used
* tools
multimeter, oscilloscope, logic analyzer, LAN analyzer
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102163908.png)

# Chapter 4. Packet analysis
* signal to binary encoding: Non-return Zero Inverted, Non-return Zero, Manchester Phase.
Encoding is handled by the Network Interface Card, or NIC, inside the computer. We can convert to ASCII using hex.

* Protocols define the structure of a message.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102175542.png)
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102175612.png)
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102175819.png)

> An Ethernet frame can hold about **1500** bytes of data. So any data that is larger than that will have to be broken apart.

* Individual packets on a large network with multiple routers can take different routes to get to the destination.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102180847.png)

# Chapter 5. Network devices and traffic
* intranet
**Hubs** and **switches** work on the local area network (LAN) or intranet, and **routers** allow us to set up wide area networks (WANs) or internets.
* hub
Hubs allow us to connect different machines on the network, such as computers and printers. A hub is a dumb device because it doesn’t understand network data, and it doesn’t know about or store MAC addresses. It simply repeats incoming signals on all ports, without making any changes to the signal before broadcasting it.
* switch
Switches allow us to connect different machines on the network, too.
Switches avoid collisions by storing and forwarding frames on the intranet. Switches are able to do this by using the MAC address of the frame. Instead of repeating the signal on all ports, it sends it on to the device that needs it. Switches think in terms of frames.
* router
Routers allow us to connect networks. If network traffic comes from a router, we can only see the router’s MAC address. All the workstations behind that router make up what we call an IP subnet.

# Chapter 6. Connecting networks with routers
* connect two networks
So how do you generally connect two networks together? The first thing you need is a working local area network (LAN). Second, you need a connection to that other network. This could be a Cat 5 cable or fiber or even a radio link. Finally, you need a router to connect the 2 networks. The router connects the networks physically as well as logically.
* MAC vs IP
A MAC address is assigned to every device connected to an Ethernet network. In your computer, that is the network card. The beginning part of the MAC address designates the manufacturer. The later part, the manufacturer increments, so all their products have unique MAC addresses. It is like a social security number, in that you really can’t tell where a person lives just by looking at it.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102184810.png)
A IP address is made up of a network address and a host address. The host part is the unique bit assigned to a particular network device. It is much like a phone number. which has a country code, area code, and local calling area—finally, your unique individual number.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102184847.png)
IP addresses give our networks a sense of location, and network nodes a sense of belonging to that location
We retrieve IP addresses using the MAC address and the Address Resolution Protocol (ARP)
* IP
An IP address has 4 octets. Each octet can represent 256 individual bits. So 4
octets of 256 each means that this type of number
can potentially represent 2^32 or 4,294,967,296 individual addresses.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102185237.png)

# Chapter 7. Routing protocols
* Routing tables tell routers where to send packets
* hops
Given a choice, the RIP protocol always chooses the route with
the fewest number of routers in its path, or the fewest number of “hops.” We call the number of hops in a route the hop-count. Unfortunately, the maximum number of hop-counts that the RIP protocol allows is 15.
* routing protocols
RIP, OSPF, EIGRP

# Chapter 8. The domain name system
Domain names are controlled be a centralized authority (called ICANN) to make sure that only one person at a time uses a domain name.
hfhealthclub.com is a domain name, while www.hfhealthclub.com is the name of a web server, www is the host name.
* DNS
DNS stands for Domain Name System. It translates fully qualified domain names that are meaningful to humans into IP addresses that computers understand. It’s a bit like an address book for the Internet that tells clients where to access resources.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190102214528.png)
* RDNS
Reverse DNS (RDNS) let's you find a domain for an IP address. If the email message comes from a domain that’s on a blocked list, then the email will be rejected. It will also be rejected if the sending server's reverse DNS lookup IP address does not match its real IP address. Spammers try to do this by using a fake domain name.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190103085050.png)

# Chapter 9. Monitoring and troubleshooting
* troubleshooting procedure
1.Start off by pinging the IP addresses of the computer’s default gateway. 2.Connect a computer to the router with a serial cable or via SSH or telnet. 3.Use the appropriate commands (like show) to look at a device’s status and counters. 4.Interpret the statistics to gain some insight on how your network is behaving.
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190103112632.png)
* SNMP
SNMP (Simple Network Management Protocol) is a way to talk to your network devices and get all kinds of information from them without having to connect each device to a laptop. 
* MIB
The information database of an SNMP managed device is called the MIB (Management Information Base).

# Chapter 10. Wireless networking
* DHCP
DHCP stands for Dynamic Host Configuration Protocol. It’s a way of automatically allowing devices onto your network with no manual intervention on your part. DHCP makes our life easy by offering to clients IP addresses it has in its pool of available addresses. 
* NAT
NAT stands for Network Address Translation, and it’s something else we can enable on the wireless access point. With NAT enabled, we can use just about any IP address we want, and however many we need to.

# Chapter 11. Network security
Set up your router’s Access Control Lists to keep attackers out.

# Chapter 12. Designing networks
a network design inlcudes: 
1. Physical layout of wire and network drops 
2. Network hardware configuration
3. Logical network design
4. An implementation plan