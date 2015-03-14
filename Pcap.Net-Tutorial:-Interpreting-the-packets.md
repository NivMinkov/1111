## Interpreting the packets

Now that we are able to capture and filter network traffic, we want to put our knowledge to work with a simple "real world" application.

In this lesson we will take code from the previous lessons and use these pieces to build a more useful program. the main purpose of the current program is to show how the protocol headers of a captured packet can be parsed and interpreted. The resulting application, called UDPdump, prints a summary of the UDP traffic on our network.

We have chosen to parse and display the UDP protocol because it is more accessible than other protocols such as TCP and consequently is an excellent initial example. Let's look at the code:

```C#
using System;
using System.Collections.Generic;
using PcapDotNet.Core;
using PcapDotNet.Packets;
using PcapDotNet.Packets.IpV4;
using PcapDotNet.Packets.Transport;

namespace InterpretingThePackets
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

            // Open the device
            using (PacketCommunicator communicator = 
                selectedDevice.Open(65536,                                  // portion of the packet to capture
                                                                            // 65536 guarantees that the whole packet will be captured on all the link layers
                                    PacketDeviceOpenAttributes.Promiscuous, // promiscuous mode
                                    1000))                                  // read timeout
            {
                // Check the link layer. We support only Ethernet for simplicity.
                if (communicator.DataLink.Kind != DataLinkKind.Ethernet)
                {
                    Console.WriteLine("This program works only on Ethernet networks.");
                    return;
                }

                // Compile the filter
                using (BerkeleyPacketFilter filter = communicator.CreateFilter("ip and udp"))
                {
                    // Set the filter
                    communicator.SetFilter(filter);
                }

                Console.WriteLine("Listening on " + selectedDevice.Description + "...");

                // start the capture
                communicator.ReceivePackets(0, PacketHandler);
            }
        }

        // Callback function invoked by libpcap for every incoming packet
        private static void PacketHandler(Packet packet)
        {
            // print timestamp and length of the packet
            Console.WriteLine(packet.Timestamp.ToString("yyyy-MM-dd hh:mm:ss.fff") + " length:" + packet.Length);

            IpV4Datagram ip = packet.Ethernet.IpV4;
            UdpDatagram udp = ip.Udp;
            
            // print ip addresses and udp ports
            Console.WriteLine(ip.Source + ":" + udp.SourcePort+ " -> " + ip.Destination + ":" + udp.DestinationPort);
        }
    }
}
```

First of all, we set the filter to "ip and udp". In this way we are sure that PacketHandler() will receive only UDP packets over IPv4: this simplifies the parsing and increases the efficiency of the program.

PacketHandler(), although limited to a single protocol dissector (UDP over IPv4), shows how simple it is to decode the network traffic using Pcap.Net. Since we aren't interested in the MAC header, directly take the IP datagram. For simplicity and before starting the capture, we check the MAC layer with DataLink property to make sure that we are dealing with an Ethernet network.

The IP datagram is located just after the MAC header. We will extract the IP source and destination addresses from the IP header.
Reaching the UDP datagram is made by a simple call to the Udp property (even though the implementation of this property is a bit more complicated, because the IP header doesn't have a fixed length). Once we have the UDP datagram, we extract the source and destination ports.

The extracted values are printed on the screen, and the result is something like:

```
2009-09-12 11:25:51.117 length:84
10.0.0.8:49003 -> 208.67.222.222:53
2009-09-12 11:25:51.212 length:125
208.67.222.222:53 -> 10.0.0.8:49003
2009-09-12 11:25:54.323 length:80
10.0.0.8:39209 -> 208.67.222.222:53
2009-09-12 11:25:54.426 length:75
10.0.0.8:47869 -> 208.67.222.222:53
2009-09-12 11:25:54.517 length:236
208.67.222.222:53 -> 10.0.0.8:39209
2009-09-12 11:25:54.621 length:91
208.67.222.222:53 -> 10.0.0.8:47869
```

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial: Filtering the traffic]] [[Next >>>|Pcap.Net Tutorial: Handling offline dump files]]