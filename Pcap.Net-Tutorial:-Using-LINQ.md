## Using LINQ

The release of Pcap.Net 0.3.0 includes a new feature within a new extensions dll that contains extensions for the Pcap.Net core functionality.  
The feature allows the user to receive packets using an IEnumerable<Packet> instance using an extension method for the PacketCommunicator that overloads ReceivePackets().

One important note regarding this overload is that it uses ReceiveSomePackets() internally in a loop. This means that if the user wants to receive N packets in the Enumerable, it doesn't mean that the method will read exactly N packets from the device. It might read more packets since ReceiveSomePackets() reads all the packets that arrive in the same buffer.  
The reason for implementing this feature this way is that receiving each packet separately will have a performance hit and for most uses it doesn't matter that a few packets will be thrown after the Enumerable was filled with the requested number of packets.  
If there is need for different way to receive packets as an Enumerable, write about it in the Discussions and I'll add more functionality.

In the following example, the user takes 100 packets that follow the BPF "ip and tcp" and selects using LINQ the timestamp and TCP options of the packets that have options.

```C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using PcapDotNet.Core;
using PcapDotNet.Core.Extensions;
using PcapDotNet.Packets;

namespace UsingLinq
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

                // Compile and set the filter
                communicator.SetFilter("ip and tcp");

                Console.WriteLine("Listening on " + selectedDevice.Description + "...");

                // start the capture
                var query = from packet in communicator.ReceivePackets(100)
                            where packet.Ethernet.IpV4.Tcp.Options.Count != 0
                            select new {packet.Timestamp, packet.Ethernet.IpV4.Tcp.Options};

                foreach (var options in query)
                    Console.WriteLine(options.Timestamp.ToString("yyyy-MM-dd hh:mm:ss.fff") + " options: " + options.Options);
            }
        }
    }
}
```

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial: Gathering Statistics on the network traffic]]