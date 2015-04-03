## Sending Packets

Although the name Pcap.Net indicates clearly that the purpose of the library is packet capture, other useful features for raw networking are provided. Among them, the user can find a complete set of functions to send packets.

Note that the original libpcap library at the moment doesn't provide any way to send packets, therefore all the functions shown here are Pcap.Net extensions based on WinPcap extensions and will not work under Unix.

### Sending a single packet with SendPacket()

The simplest way to send a packet is shown in the following code snippet. After opening an adapter, SendPacket() is called to send a hand-crafted packet. SendPacket() takes as argument the packet containing the data to send. Notice that the packet is sent to the net as is, without any manipulation. This means that the application has to create the correct protocol headers in order to send something meaningful.

This code uses SendPacket() to send 100 Ping (ICMP Echo) packets to different IPv4 destinations, with different IPv4 IDs, different ICMP Sequence Numbers and different ICMP Identifiers. This is done by creating a PacketBuilder instance with Ethernet, IPv4 and ICMP Echo layers, and by changing the layers' properties' values for each packet.
In addition, the code uses SendPacket() to send various packets. The creation of these packets is in the different Build...Packet() methods, and this can give you an overlook of the different packets you can create using Pcap.Net.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using PcapDotNet.Base;
using PcapDotNet.Core;
using PcapDotNet.Packets;
using PcapDotNet.Packets.Arp;
using PcapDotNet.Packets.Dns;
using PcapDotNet.Packets.Ethernet;
using PcapDotNet.Packets.Gre;
using PcapDotNet.Packets.Http;
using PcapDotNet.Packets.Icmp;
using PcapDotNet.Packets.Igmp;
using PcapDotNet.Packets.IpV4;
using PcapDotNet.Packets.IpV6;
using PcapDotNet.Packets.Transport;

namespace SendingASinglePacketWithSendPacket
{
    class Program
    {
        static void Main(string[] args)
        {
            // Retrieve the device list from the local machine
            IList<LivePacketDevice> allDevices = LivePacketDevice.AllLocalMachine;

            if (allDevices.Count == 0)
            {
                Console.WriteLine("No interfaces found! Make sure WinPcap is installed.");
                return;
            }

            // Print the list
            for (int i = 0; i != allDevices.Count; ++i)
            {
                LivePacketDevice device = allDevices[i];
                Console.Write((i + 1) + ". " + device.Name);
                if (device.Description != null)
                    Console.WriteLine(" (" + device.Description + ")");
                else
                    Console.WriteLine(" (No description available)");
            }

            int deviceIndex = 0;
            do
            {
                Console.WriteLine("Enter the interface number (1-" + allDevices.Count + "):");
                string deviceIndexString = Console.ReadLine();
                if (!int.TryParse(deviceIndexString, out deviceIndex) ||
                    deviceIndex < 1 || deviceIndex > allDevices.Count)
                {
                    deviceIndex = 0;
                }
            } while (deviceIndex == 0);

            // Take the selected adapter
            PacketDevice selectedDevice = allDevices[deviceIndex - 1];

            // Open the output device
            using (PacketCommunicator communicator = selectedDevice.Open(100, // name of the device
                                                                         PacketDeviceOpenAttributes.Promiscuous, // promiscuous mode
                                                                         1000)) // read timeout
            {
                // Supposing to be on ethernet, set mac source to 01:01:01:01:01:01
                MacAddress source = new MacAddress("01:01:01:01:01:01");

                // set mac destination to 02:02:02:02:02:02
                MacAddress destination = new MacAddress("02:02:02:02:02:02");

                // Create the packets layers

                // Ethernet Layer
                EthernetLayer ethernetLayer = new EthernetLayer
                                                  {
                                                      Source = source,
                                                      Destination = destination
                                                  };

                // IPv4 Layer
                IpV4Layer ipV4Layer = new IpV4Layer
                                          {
                                              Source = new IpV4Address("1.2.3.4"),
                                              Ttl = 128,
                                              // The rest of the important parameters will be set for each packet
                                          };

                // ICMP Layer
                IcmpEchoLayer icmpLayer = new IcmpEchoLayer();

                // Create the builder that will build our packets
                PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, icmpLayer);

                // Send 100 Pings to different destination with different parameters
                for (int i = 0; i != 100; ++i)
                {
                    // Set IPv4 parameters
                    ipV4Layer.CurrentDestination = new IpV4Address("2.3.4." + i);
                    ipV4Layer.Identification = (ushort)i;

                    // Set ICMP parameters
                    icmpLayer.SequenceNumber = (ushort)i;
                    icmpLayer.Identifier = (ushort)i;

                    // Build the packet
                    Packet packet = builder.Build(DateTime.Now);

                    // Send down the packet
                    communicator.SendPacket(packet);
                }

                communicator.SendPacket(BuildEthernetPacket());
                communicator.SendPacket(BuildArpPacket());
                communicator.SendPacket(BuildVLanTaggedFramePacket());
                communicator.SendPacket(BuildIpV4Packet());
                communicator.SendPacket(BuildIpV6Packet());
                communicator.SendPacket(BuildIcmpPacket());
                communicator.SendPacket(BuildIgmpPacket());
                communicator.SendPacket(BuildGrePacket());
                communicator.SendPacket(BuildUdpPacket());
                communicator.SendPacket(BuildTcpPacket());
                communicator.SendPacket(BuildDnsPacket());
                communicator.SendPacket(BuildHttpPacket());
                communicator.SendPacket(BuildComplexPacket());
            }
        }

        /// <summary>
        /// This function build an Ethernet with payload packet.
        /// </summary>
        private static Packet BuildEthernetPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.IpV4,
                    };

            PayloadLayer payloadLayer =
                new PayloadLayer
                    {
                        Data = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, payloadLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an ARP over Ethernet packet.
        /// </summary>
        private static Packet BuildArpPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            ArpLayer arpLayer =
                new ArpLayer
                    {
                        ProtocolType = EthernetType.IpV4,
                        Operation = ArpOperation.Request,
                        SenderHardwareAddress = new byte[] {3, 3, 3, 3, 3, 3}.AsReadOnly(), // 03:03:03:03:03:03.
                        SenderProtocolAddress = new byte[] {1, 2, 3, 4}.AsReadOnly(), // 1.2.3.4.
                        TargetHardwareAddress = new byte[] {4, 4, 4, 4, 4, 4}.AsReadOnly(), // 04:04:04:04:04:04.
                        TargetProtocolAddress = new byte[] {11, 22, 33, 44}.AsReadOnly(), // 11.22.33.44.
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, arpLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build a VLanTaggedFrame over Ethernet with payload packet.
        /// </summary>
        private static Packet BuildVLanTaggedFramePacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            VLanTaggedFrameLayer vLanTaggedFrameLayer =
                new VLanTaggedFrameLayer
                    {
                        PriorityCodePoint = ClassOfService.Background,
                        CanonicalFormatIndicator = false,
                        VLanIdentifier = 50,
                        EtherType = EthernetType.IpV4,
                    };

            PayloadLayer payloadLayer =
                new PayloadLayer
                    {
                        Data = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, vLanTaggedFrameLayer, payloadLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an IPv4 over Ethernet with payload packet.
        /// </summary>
        private static Packet BuildIpV4Packet()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None,
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = IpV4Protocol.Udp,
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            PayloadLayer payloadLayer =
                new PayloadLayer
                    {
                        Data = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, payloadLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an IPv6 over Ethernet with payload packet.
        /// </summary>
        private static Packet BuildIpV6Packet()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                {
                    Source = new MacAddress("01:01:01:01:01:01"),
                    Destination = new MacAddress("02:02:02:02:02:02"),
                    EtherType = EthernetType.None,
                };

            IpV6Layer ipV6Layer =
                new IpV6Layer
                {
                    Source = new IpV6Address("0123:4567:89AB:CDEF:0123:4567:89AB:CDEF"),
                    CurrentDestination = new IpV6Address("FEDC:BA98:7654:3210:FEDC:BA98:7654:3210"),
                    FlowLabel = 123,
                    HopLimit = 100,
                    NextHeader = IpV4Protocol.Udp,
                };

            PayloadLayer payloadLayer =
                new PayloadLayer
                {
                    Data = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV6Layer, payloadLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an ICMP over IPv4 over Ethernet packet.
        /// </summary>
        private static Packet BuildIcmpPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            IcmpEchoLayer icmpLayer =
                new IcmpEchoLayer
                    {
                        Checksum = null, // Will be filled automatically.
                        Identifier = 456,
                        SequenceNumber = 800,
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, icmpLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an IGMP over IPv4 over Ethernet packet.
        /// </summary>
        private static Packet BuildIgmpPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            IgmpQueryVersion1Layer igmpLayer =
                new IgmpQueryVersion1Layer
                    {
                        GroupAddress = new IpV4Address("1.2.3.4"),
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, igmpLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an IPv4 over GRE over IPv4 over Ethernet packet.
        /// </summary>
        private static Packet BuildGrePacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            GreLayer greLayer =
                new GreLayer
                    {
                        Version = GreVersion.Gre,
                        ProtocolType = EthernetType.None, // Will be filled automatically.
                        RecursionControl = 0,
                        FutureUseBits = 0,
                        ChecksumPresent = true,
                        Checksum = null, // Will be filled automatically.
                        Key = null,
                        SequenceNumber = 123,
                        AcknowledgmentSequenceNumber = null,
                        RoutingOffset = null,
                        Routing = null,
                        StrictSourceRoute = false,
                    };

            IpV4Layer innerIpV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("100.200.201.202"),
                        CurrentDestination = new IpV4Address("123.254.132.40"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = IpV4Protocol.Udp,
                        Ttl = 120,
                        TypeOfService = 0,
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, greLayer, innerIpV4Layer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an UDP over IPv4 over Ethernet with payload packet.
        /// </summary>
        private static Packet BuildUdpPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            UdpLayer udpLayer =
                new UdpLayer
                    {
                        SourcePort = 4050,
                        DestinationPort = 25,
                        Checksum = null, // Will be filled automatically.
                        CalculateChecksumValue = true,
                    };

            PayloadLayer payloadLayer =
                new PayloadLayer
                    {
                        Data = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, udpLayer, payloadLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an TCP over IPv4 over Ethernet with payload packet.
        /// </summary>
        private static Packet BuildTcpPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            TcpLayer tcpLayer =
                new TcpLayer
                    {
                        SourcePort = 4050,
                        DestinationPort = 25,
                        Checksum = null, // Will be filled automatically.
                        SequenceNumber = 100,
                        AcknowledgmentNumber = 50,
                        ControlBits = TcpControlBits.Acknowledgment,
                        Window = 100,
                        UrgentPointer = 0,
                        Options = TcpOptions.None,
                    };

            PayloadLayer payloadLayer =
                new PayloadLayer
                    {
                        Data = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, tcpLayer, payloadLayer);

            return builder.Build(DateTime.Now);
        }
        
        /// <summary>
        /// This function build a DNS over UDP over IPv4 over Ethernet packet.
        /// </summary>
        private static Packet BuildDnsPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            UdpLayer udpLayer =
                new UdpLayer
                    {
                        SourcePort = 4050,
                        DestinationPort = 53,
                        Checksum = null, // Will be filled automatically.
                        CalculateChecksumValue = true,
                    };

            DnsLayer dnsLayer =
                new DnsLayer
                    {
                        Id = 100,
                        IsResponse = false,
                        OpCode = DnsOpCode.Query,
                        IsAuthoritativeAnswer = false,
                        IsTruncated = false,
                        IsRecursionDesired = true,
                        IsRecursionAvailable = false,
                        FutureUse = false,
                        IsAuthenticData = false,
                        IsCheckingDisabled = false,
                        ResponseCode = DnsResponseCode.NoError,
                        Queries = new[]
                                      {
                                          new DnsQueryResourceRecord(new DnsDomainName("pcapdot.net"),
                                                                     DnsType.A,
                                                                     DnsClass.Internet),
                                      },
                        Answers = null,
                        Authorities = null,
                        Additionals = null,
                        DomainNameCompressionMode = DnsDomainNameCompressionMode.All,
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, udpLayer, dnsLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build an HTTP over TCP over IPv4 over Ethernet packet.
        /// </summary>
        private static Packet BuildHttpPacket()
        {
            EthernetLayer ethernetLayer =
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    };

            IpV4Layer ipV4Layer =
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    };

            TcpLayer tcpLayer =
                new TcpLayer
                    {
                        SourcePort = 4050,
                        DestinationPort = 80,
                        Checksum = null, // Will be filled automatically.
                        SequenceNumber = 100,
                        AcknowledgmentNumber = 50,
                        ControlBits = TcpControlBits.Acknowledgment,
                        Window = 100,
                        UrgentPointer = 0,
                        Options = TcpOptions.None,
                    };

            HttpRequestLayer httpLayer =
                new HttpRequestLayer
                    {
                        Version = HttpVersion.Version11,
                        Header = new HttpHeader(new HttpContentLengthField(11)),
                        Body = new Datagram(Encoding.ASCII.GetBytes("hello world")),
                        Method = new HttpRequestMethod(HttpRequestKnownMethod.Get),
                        Uri = @"http://pcapdot.net/",
                    };

            PacketBuilder builder = new PacketBuilder(ethernetLayer, ipV4Layer, tcpLayer, httpLayer);

            return builder.Build(DateTime.Now);
        }

        /// <summary>
        /// This function build a DNS over UDP over IPv4 over GRE over IPv4 over IPv4 over VLAN Tagged Frame over VLAN Tagged Frame over Ethernet.
        /// </summary>
        private static Packet BuildComplexPacket()
        {
            return PacketBuilder.Build(
                DateTime.Now,
                new EthernetLayer
                    {
                        Source = new MacAddress("01:01:01:01:01:01"),
                        Destination = new MacAddress("02:02:02:02:02:02"),
                        EtherType = EthernetType.None, // Will be filled automatically.
                    },
                new VLanTaggedFrameLayer
                    {
                        PriorityCodePoint = ClassOfService.ExcellentEffort,
                        CanonicalFormatIndicator = false,
                        EtherType = EthernetType.None, // Will be filled automatically.
                    },
                new VLanTaggedFrameLayer
                    {
                        PriorityCodePoint = ClassOfService.BestEffort,
                        CanonicalFormatIndicator = false,
                        EtherType = EthernetType.None, // Will be filled automatically.
                    },
                new IpV4Layer
                    {
                        Source = new IpV4Address("1.2.3.4"),
                        CurrentDestination = new IpV4Address("11.22.33.44"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = IpV4Options.None,
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    },
                new IpV4Layer
                    {
                        Source = new IpV4Address("5.6.7.8"),
                        CurrentDestination = new IpV4Address("55.66.77.88"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 456,
                        Options = new IpV4Options(new IpV4OptionStrictSourceRouting(
                                                      new[]
                                                          {
                                                              new IpV4Address("100.200.100.200"),
                                                              new IpV4Address("150.250.150.250")
                                                          }, 1)),
                        Protocol = null, // Will be filled automatically.
                        Ttl = 200,
                        TypeOfService = 0,
                    },
                new GreLayer
                    {
                        Version = GreVersion.Gre,
                        ProtocolType = EthernetType.None, // Will be filled automatically.
                        RecursionControl = 0,
                        FutureUseBits = 0,
                        ChecksumPresent = true,
                        Checksum = null, // Will be filled automatically.
                        Key = 100,
                        SequenceNumber = 123,
                        AcknowledgmentSequenceNumber = null,
                        RoutingOffset = null,
                        Routing = new[]
                                      {
                                          new GreSourceRouteEntryIp(
                                              new[]
                                                  {
                                                      new IpV4Address("10.20.30.40"),
                                                      new IpV4Address("40.30.20.10")
                                                  }.AsReadOnly(), 1),
                                          new GreSourceRouteEntryIp(
                                              new[]
                                                  {
                                                      new IpV4Address("11.22.33.44"),
                                                      new IpV4Address("44.33.22.11")
                                                  }.AsReadOnly(), 0)
                                      }.Cast<GreSourceRouteEntry>().ToArray().AsReadOnly(),
                        StrictSourceRoute = false,
                    },
                new IpV4Layer
                    {
                        Source = new IpV4Address("51.52.53.54"),
                        CurrentDestination = new IpV4Address("61.62.63.64"),
                        Fragmentation = IpV4Fragmentation.None,
                        HeaderChecksum = null, // Will be filled automatically.
                        Identification = 123,
                        Options = new IpV4Options(
                            new IpV4OptionTimestampOnly(0, 1,
                                                        new IpV4TimeOfDay(new TimeSpan(1, 2, 3)),
                                                        new IpV4TimeOfDay(new TimeSpan(15, 55, 59))),
                            new IpV4OptionQuickStart(IpV4OptionQuickStartFunction.RateRequest, 10, 200, 300)),
                        Protocol = null, // Will be filled automatically.
                        Ttl = 100,
                        TypeOfService = 0,
                    },
                new UdpLayer
                    {
                        SourcePort = 53,
                        DestinationPort = 40101,
                        Checksum = null, // Will be filled automatically.
                        CalculateChecksumValue = true,
                    },
                new DnsLayer
                    {
                        Id = 10012,
                        IsResponse = true,
                        OpCode = DnsOpCode.Query,
                        IsAuthoritativeAnswer = true,
                        IsTruncated = false,
                        IsRecursionDesired = true,
                        IsRecursionAvailable = true,
                        FutureUse = false,
                        IsAuthenticData = true,
                        IsCheckingDisabled = false,
                        ResponseCode = DnsResponseCode.NoError,
                        Queries =
                            new[]
                                {
                                    new DnsQueryResourceRecord(
                                        new DnsDomainName("pcapdot.net"),
                                        DnsType.Any,
                                        DnsClass.Internet),
                                },
                        Answers =
                            new[]
                                {
                                    new DnsDataResourceRecord(
                                        new DnsDomainName("pcapdot.net"),
                                        DnsType.A,
                                        DnsClass.Internet
                                        , 50000,
                                        new DnsResourceDataIpV4(new IpV4Address("10.20.30.44"))),
                                    new DnsDataResourceRecord(
                                        new DnsDomainName("pcapdot.net"),
                                        DnsType.Txt,
                                        DnsClass.Internet,
                                        50000,
                                        new DnsResourceDataText(new[] {new DataSegment(Encoding.ASCII.GetBytes("Pcap.Net"))}.AsReadOnly()))
                                },
                        Authorities =
                            new[]
                                {
                                    new DnsDataResourceRecord(
                                        new DnsDomainName("pcapdot.net"),
                                        DnsType.MailExchange,
                                        DnsClass.Internet,
                                        100,
                                        new DnsResourceDataMailExchange(100, new DnsDomainName("pcapdot.net")))
                                },
                        Additionals =
                            new[]
                                {
                                    new DnsOptResourceRecord(
                                        new DnsDomainName("pcapdot.net"),
                                        50000,
                                        0,
                                        DnsOptVersion.Version0,
                                        DnsOptFlags.DnsSecOk,
                                        new DnsResourceDataOptions(
                                            new DnsOptions(
                                                new DnsOptionUpdateLease(100),
                                                new DnsOptionLongLivedQuery(1,
                                                                            DnsLongLivedQueryOpCode.Refresh,
                                                                            DnsLongLivedQueryErrorCode.NoError,
                                                                            10, 20))))
                                },
                        DomainNameCompressionMode = DnsDomainNameCompressionMode.All,
                    });
        }
    }
}
```

### Sending packets using Send Buffer

While SendPacket() offers a simple and immediate way to send a single packet, send buffers provides an advanced, powerful and optimized mechanism to send a collection of packets. A send buffer is a container for a variable number of packets that will be sent to the network. It has a size, that represents the maximum amount of bytes it can store.

A send buffer is created calling the PacketSendBuffer constructor, specifying the size of the new send buffer.

Once the send buffer is created, Enqueue() can be used to add a packet to the send buffer. This function takes a packet with data and timestamp.

To transmit a send buffer, Pcap.Net provides the Transmit() method. Note the second parameter: if true, the send will be synchronized, i.e. the relative timestamps of the packets will be respected. This operation requires a remarkable amount of CPU, because the synchronization takes place in the kernel driver using "busy wait" loops. Although this operation is quite CPU intensive, it often results in very high precision packet transmissions (often around few microseconds or less).

Note that transmitting a send buffer with Transmit() is much more efficient than performing a series of SendPacket(), because the send buffer is buffered at kernel level drastically decreasing the number of context switches.

When a send buffer is no longer needed, it should be disposed by calling Dispose() that frees all the buffers associated with the send buffer.

The next program shows how to use send buffers. It opens a capture file, then it moves the packets from the file to a properly allocated send buffer. At this point it transmits the buffer, synchronizing it if requested by the user.

Note that the link-layer of the dumpfile is compared with the one of the interface that will send the packets using DataLink, and a warning is printed if they are different -- it is important that the capture-file link-layer be the same as the adapter's link layer for otherwise the transmission is pointless.

```C#
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.IO;
using PcapDotNet.Core;
using PcapDotNet.Packets;

namespace SendingPacketsUsingSendBuffer
{
    class Program
    {
        static void Main(string[] args)
        {
            // Check the validity of the command line
            if (args.Length == 0 || args.Length > 2)
            {
                Usage();
                return;
            }

            // Retrieve the device list from the local machine
            IList<LivePacketDevice> allDevices = LivePacketDevice.AllLocalMachine;

            if (allDevices.Count == 0)
            {
                Console.WriteLine("No interfaces found! Make sure WinPcap is installed.");
                return;
            }

            // Print the list
            for (int i = 0; i != allDevices.Count; ++i)
            {
                LivePacketDevice device = allDevices[i];
                Console.Write((i + 1) + ". " + device.Name);
                if (device.Description != null)
                    Console.WriteLine(" (" + device.Description + ")");
                else
                    Console.WriteLine(" (No description available)");
            }

            int deviceIndex = 0;
            do
            {
                Console.WriteLine("Enter the interface number (1-" + allDevices.Count + "):");
                string deviceIndexString = Console.ReadLine();
                if (!int.TryParse(deviceIndexString, out deviceIndex) ||
                    deviceIndex < 1 || deviceIndex > allDevices.Count)
                {
                    deviceIndex = 0;
                }
            } while (deviceIndex == 0);

            // Take the selected adapter
            PacketDevice selectedOutputDevice = allDevices[deviceIndex - 1];

            // Retrieve the length of the capture file
            long capLength = new FileInfo(args[0]).Length;

            // Chek if the timestamps must be respected
            bool isSync = (args.Length == 2 && args[1][0] == 's');

            // Open the capture file
            OfflinePacketDevice selectedInputDevice = new OfflinePacketDevice(args[0]);

            using (PacketCommunicator inputCommunicator =
                selectedInputDevice.Open(65536, // portion of the packet to capture
                // 65536 guarantees that the whole packet will be captured on all the link layers
                                         PacketDeviceOpenAttributes.Promiscuous, // promiscuous mode
                                         1000)) // read timeout
            {
                using (PacketCommunicator outputCommunicator =
                    selectedOutputDevice.Open(100, PacketDeviceOpenAttributes.Promiscuous, 1000))
                {
                    // Check the MAC type
                    if (inputCommunicator.DataLink != outputCommunicator.DataLink)
                    {
                        Console.WriteLine(
                            "Warning: the datalink of the capture differs from the one of the selected interface.");
                        Console.WriteLine("Press a key to continue, or CTRL+C to stop.");
                        Console.ReadKey();
                    }

                    // Allocate a send buffer
                    using (PacketSendBuffer sendBuffer = new PacketSendBuffer((uint)capLength))
                    {
                        // Fill the buffer with the packets from the file
                        int numPackets = 0;
                        Packet packet;
                        while (inputCommunicator.ReceivePacket(out packet) == PacketCommunicatorReceiveResult.Ok)
                        {
                            sendBuffer.Enqueue(packet);
                            ++numPackets;
                        }

                        // Transmit the queue
                        Stopwatch stopwatch = new Stopwatch();
                        stopwatch.Start();
                        long startTimeMs = stopwatch.ElapsedMilliseconds;
                        Console.WriteLine("Start Time: " + startTimeMs);
                        outputCommunicator.Transmit(sendBuffer, isSync);
                        long endTimeMs = stopwatch.ElapsedMilliseconds;
                        Console.WriteLine("End Time: " + endTimeMs);
                        long elapsedTimeMs = endTimeMs - startTimeMs;
                        Console.WriteLine("Elapsed Time: " + elapsedTimeMs);
                        double averagePacketsPerSecond = elapsedTimeMs == 0 ? double.MaxValue : (double)numPackets / elapsedTimeMs * 1000;

                        Console.WriteLine("Elapsed time: " + elapsedTimeMs + " ms");
                        Console.WriteLine("Total packets generated = " + numPackets);
                        Console.WriteLine("Average packets per second = " + averagePacketsPerSecond);
                        Console.WriteLine();
                    }
                }
            }
        }

        private static void Usage()
        {
            Console.WriteLine("Sends a libpcap/tcpdump capture file to the net.");
            Console.WriteLine("Usage:");
            Console.WriteLine("\t" + Environment.GetCommandLineArgs()[0] + " <filename> [s]");
            Console.WriteLine();
            Console.WriteLine("Parameters:");
            Console.WriteLine("\tfilename: the name of the dump file that will be sent to the network");
            Console.WriteLine(
                "\ts: if present, forces the packets to be sent synchronously, i.e. respecting the timestamps in the dump file. This option will work only under Windows NTx");
        }
    }
}
```

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial Handling offline dump files]] [[Next >>>|Pcap.Net Tutorial Gathering Statistics on the network traffic]]