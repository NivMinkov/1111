Welcome to **Pcap.Net** - the open-source, [.NET][dotnet] wrapper for [WinPcap][winpcap] written in [C++/CLI][cpluspluscli] and [C#][csharp], which features almost all WinPcap features and includes a packet interpretation framework.

This wiki is the main source of documentation for users and developers of Pcap.Net.

## Quick navigation

| Using Pcap.Net                             | Developing Pcap.Net     |
|--------------------------------------------|-------------------------|
| [[User Guide]]                             | [[Developer Guide]]     |
| Tutorial and user guide for using Pcap.Net | How to change Pcap.Net  |

## Need help?
Use the [Pcap.Net Q&A Group](https://groups.google.com/forum/#!forum/pcapdotnet) to ask questions.

## Features

### .Net wrap for WinPcap

Including:
* Getting the list of Live Devices on the local host.
* Reading packets from Live Devices (Network Devices) and Offline Devices (Files) using the different WinPcap methods.
* Receiving statistics on the entire capture.
* Receiving statistics of packets instead of the full packets.
* Using different sampling methods.
* Applying Berkley Packet Filters.
* Sending packets to Live Devices directly or using WinPcap's send queues.
* Dumping packets to Pcap files.
* Using Enumerables to receive packets (and LINQ).
Not including:
* AirPcap features.
* Remote Pcap features.

### Packet interpretation
* Ethernet + VLAN tagging (802.1Q)
* ARP
* IPv4
* IPv6
* GRE
* ICMP
* IGMP
* UDP
* TCP
* DNS
* HTTP

[dotnet]: http://www.microsoft.com/net
[winpcap]: http://www.winpcap.org/
[cpluspluscli]: http://en.wikipedia.org/wiki/C%2B%2B/CLI
[csharp]: http://en.wikipedia.org/wiki/C_Sharp_%28programming_language%29
