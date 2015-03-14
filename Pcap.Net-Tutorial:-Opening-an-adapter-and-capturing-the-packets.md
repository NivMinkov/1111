## Opening an adapter and capturing the packets

Now that we've seen how to obtain an adapter to play with, let's start the real job, opening an adapter and capturing some traffic. In this lesson we'll write a program that prints some information about each packet flowing through the adapter.

The function that opens a capture device is Open(). The parameters, snapshotLength, attributes and readTimeout deserve some explanation.
* **snapshotLength** specifies the portion of the packet to capture. On some OSes (like xBSD and Win32), the packet driver can be configured to capture only the initial part of any packet: this decreases the amount of data to copy to the application and therefore improves the efficiency of the capture. In this case we use the value 65536 which is higher than the greatest MTU that we could encounter. In this manner we ensure that the application will always receive the whole packet.
* **attributes**: the most important flag is the one that indicates if the adapter will be put in promiscuous mode. In normal operation, an adapter only captures packets from the network that are destined to it; the packets exchanged by other hosts are therefore ignored. Instead, when the adapter is in promiscuous mode it captures all packets whether they are destined to it or not. This means that on shared media (like non-switched Ethernet), Pcap.Net will be able to capture the packets of other hosts. Promiscuous mode is the default for most capture applications, so we enable it in the following example.
* **readTimeout** specifies the read timeout, in milliseconds. A read on the adapter (for example, with ReceiveSomePackets() or ReceivePacket()) will always return after readTimeout milliseconds, even if no packets are available from the network. readTimeout also defines the interval between statistical reports if the adapter is in statistical mode (see the lesson [[Gathering Statistics on the network traffic|Pcap.Net Tutorial: Gathering Statistics on the network traffic]] for information about statistical mode). Setting readTimeout to 0 means no timeout, a read on the adapter never returns if no packets arrive. A -1 timeout on the other side causes a read on the adapter to always return immediately.

```C#
using System;
using System.Collections.Generic;
using PcapDotNet.Core;
using PcapDotNet.Packets;

namespace OpeningAnAdapterAndCapturingThePackets
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
                Console.WriteLine("Listening on " + selectedDevice.Description + "...");

                // start the capture
                communicator.ReceivePackets(0, PacketHandler);
            }
        }

        // Callback function invoked by Pcap.Net for every incoming packet
        private static void PacketHandler(Packet packet)
        {
            Console.WriteLine(packet.Timestamp.ToString("yyyy-MM-dd hh:mm:ss.fff") + " length:" + packet.Length);
        }
    }
}
```

Once the adapter is opened, the capture can be started with ReceiveSomePackets() or ReceivePackets(). These two functions are very similar, the difference is that ReceiveSomePackets() returns (although not guaranteed) when the timeout expires while ReceivePackets() doesn't return until count packets have been captured, so it can block for an arbitrary period on an under-utilized network. ReceivePackets() is enough for the purpose of this sample, while ReceiveSomePackets() is normally used in a more complex program.

Both of these functions have a callback parameter, HandlePacket callback, delegating a function that will receive the packets. This function is invoked by Pcap.Net for every new packet coming from the network and receives a packet with some information like the timestamp, the length and the actual data of the packet including all the protocol headers. Note that the frame CRC is normally not present, because it is removed by the network adapter after frame validation. Note also that most adapters discard packets with wrong CRCs, therefore Pcap.Net is normally not able to capture them.

The above example extracts the timestamp and the length of every packet and prints them on the screen.

Please note that there may be a drawback using ReceivePackets() mainly related to the fact that the handler is called by the packet capture driver; therefore the user application does not have direct control over it. Another approach (and to have more readable programs) is to use the ReceivePacket() function, which is presented in the next example (Capturing the packets without the callback).

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial: Obtaining advanced information about installed devices]] [[Next >>>|Pcap.Net Tutorial: Capturing the packets without the callback]]