## Windows Bugs
* Time Zones bug (Reported May 14th, 2010): https://connect.microsoft.com/VisualStudio/feedback/details/559198/net-4-datetime-tolocaltime-is-sometimes-wrong (Microsoft deleted that page).

## WinPcap Bugs
* WinPcap's driver bug that causes BSOD in Windows (Reported November 12th, 2009): <http://www.winpcap.org/pipermail/winpcap-bugs/2009-November/001094.html>, <http://www.winpcap.org/misc/changelog.htm>

## Wireshark Bugs
* IPv6 RPL Routing Header with length of 8 bytes still reads an address (Reported November 28th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=11803>
* IP "next protocol" value of 80 needs to support encapsulations other than RFC 1070 (Reported November 28th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=11802>
* Wireshark doesn't parse an HTTP packet as chunked (Reported November 28th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=11801>
* IGMP Type appears twice and empty Data is shown (Reported October 10th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=11582>
* ICMP Redirect takes 4 bytes for IPv4 payload instead of 8 (Reported February 21st, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10992>
* Length of original datagram in ICMP Parameter Problem message is treated as the total IPv4 length (Reported February 21st, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10991>
* Wireshark ignores DNS Client Subnet option's data length when it's too long (Reported February 21st, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10988>
* IPv6 Local Mobility Anchor Address mobility option code is treated incorrectly (Reported February 14th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10961>
* DNS LOC Precision missing units (Reported February 7th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10940>
* ICMP Parameter Problem message contains "Length of original datagram" (Reported February 7th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10939>
* HTTP chunked response includes data beyond the chunked response (Reported November 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10707>
* NLPID not shown for protocols where the NLPID is not part of the PDU (Reported November 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10706>
* IPv6 Experimental mobility header data is interpreted as options (Reported November 14th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10703>
* DNS NAPTR RR Replacement Length is incorrect (Reported November 14th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10700>
* IPv6 Experimental mobility option includes too many bytes for the Data field (Reported November 8th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10682>
* UTF-8 replacement characters in FT_STRINGs are escaped for presentation (Reported November 8th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10681>
* IPv6 Mobility Option Context Request reads an extra request (Reported November 7th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10676>
* DNS WKS RR Protocol field is read as 4 bytes instead of 1 (Reported November 7th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10675>
* DNS Name Length for Zone RR on root is 6 and Label Count is 1 (Reported November 7th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10674>
* IPv6 CGA Parameters mobility option includes too many bytes for CGA Parameters field (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10654>
* IPv6 Signature mobility option includes too many bytes for CGA Parameters field (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10653>
* DNS A6 Address Suffix field is parsed incorrectly (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10652>
* TShark crashes when running with PDML on a specific packet (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10651>
* DNS ISDN RR Sub Address field is read one byte early (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10650>
* IPv6 Local Mobility Anchor Address mobility option's code and reserved fields are parsed as 2 bytes instead of 1 (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10630>
* IPv6 DNS-UPDATE-TYPE mobility option includes too many bytes for the MD identity field (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10629>
* IPv6 Mobility Header Link-Layer Address Mobility Option is parsed incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10627>
* IPv6 AUTH mobility option parses Mobility SPI and Authentication Data incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10626>
* IPv6 MESG-ID mobility option is parsed incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10625>
* IPv6 Care Of Test mobility option includes too many bytes for the Keygen Token field (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10624>
* IPv6 Redirect Mobility Option K and N bits are parsed incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10622>
* IPv6 Permanent Home Keygen Token mobility option includes too many bytes for the token field (Reported October 24th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10619>
* IPv6 Vendor Specific Mobility Option includes the next mobility option type (Reported October 24th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10618>
* DNS NXT RR is parsed incorrectly (Reported October 24th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10615>
* IPv6 Mobility Option Mobile Node Link Layer Identifier Link-layer Identifier field is read beyond the option data (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10578>
* IPv6 Mobility Option Binding Authorization Data for FMIPv6 Authenticator field is read beyond the option data (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10577>
* IPv6 Mobility Option IPv6 Address/Prefix marks too many bytes for the address/prefix field (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10576>
* IPv6 QuickStart option Nonce is read incorrectly (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10575>
* IPv6 Calipso option Compartment Length is calculated incorrectly (Reported October 11th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10561>
* Last Address field for IPv6 RPL routing header is interpreted incorrectly (Reported October 11th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10560>
* IPv6 RPL option is read as less bytes than it is (Reported October 11th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10559>
* IPv4 Protocols names: IDPR and IDRP (Reported May 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10110>
* IPv6 Mobility Option Service Selection with option length = 1 is considered invalid (Reported April 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10045>
* IPv6 Mobility Option Link Layer Address parses 0 length address as if it is of length 1 (Reported April 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10043>
* IPv6 Mobility Option Binding Revocation recognize second flag as Acknowledge (Reported April 21th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10015>
* IPv6 Mobility Header Binding Revocation Acknowledge Global field is the wrong bit (Reported April 18th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10007>
* IPv6 Mobility Header Link Layer Address is parsed incorrectly (Reported April 18th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10006>
* IPv6 Authentication Header not parsed after Mobility Header (Reported April 18th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10005>
* IPv6 Next Header is Unknown yet Wireshark tries parsing an IPv6 Extension Header (Reported April 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=9996>
* IPv6 Next Header 0x3d recognized as SHIM6 (Reported April 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=9995>
* The basic security IP option is implemented according to RFC 791 instead of RFC 1108 (Reported April 10th, 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=7064>
* DNS OPT flags are not parsed correctly (Reported April 8th, 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=7045>
* IPv4 destination is calculated incorrectly when there are bad options before source routing options (Reported April 8th, 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=7043>
* DNS KEY has an extra "Key id" field (Reported February 6th , 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=6704>
* IPv4 checksum says "not all data available" when all data is available (Reported September 9th, 2010): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=5194>
* HTTP empty URIs (Reported September 5th, 2010): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=5181>
* Packet causes Wireshark to crash (Reported August 21st, 2009): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=3925>