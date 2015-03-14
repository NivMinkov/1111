**Question 1:** Can I use Pcap.Net to drop/alter the incoming/outgoing packets? Is it possible to use Pcap.Net to build a firewall?  
**Answer:** No. See [WinPcap answer](http://www.winpcap.org/misc/faq.htm#Q-17).

**Question 2:** Why does Pcap.Net fails when trying to open and close over about 500 files?  
**Answer:** Starting Changeset 72641, it is possible to open unlimited number of files. However, if the filename isn't ASCII or ISO-8859-1 charsets, it would fail because of a [WinPcap issue](http://www.winpcap.org/pipermail/winpcap-bugs/2012-December/001547.html).

**Question 3:** How can I convert a DataSegment (or a Datagram) to byte[]?  
**Answer:** Just use ToMemoryStream() or Write() methods.

**Question 4:** How can I do IPv4 packet defragmentation using Pcap.Net?  
**Answer:** All of Pcap.Net features on per packet basis. IPv4 defragmentation is currently not implemented within Pcap.Net. Anyone can use Pcap.Net, and following the different IPv4 field (like identifier and fragmentation offset) build a reconstructed IPv4 packet.

**Question 5:** How can I do TCP reconstruction using Pcap.Net?  
**Answer:** All of Pcap.Net features on per packet basis. TCP session reconstruction is currently not implemented within Pcap.Net. Anyone can use Pcap.Net, and following the different TCP field (like sequence and acknowledgement numbers) build a reconstructed TCP session.

**Question 6:** I'm getting a "Could not load file or assembly 'PcapDotNet.Core.dll' or one of its dependencies. The specified module could not be found." error. What should I do?  
**Answer:** Make sure that you followed the Pcap.Net User Guide. Specifically, see the "Running Pcap.Net applications" section in [[Using Pcap.Net in your programs]].