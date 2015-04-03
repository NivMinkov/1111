## Capturing the packets without the callback

The example program in this lesson behaves exactly like the previous program ([[Opening an adapter and capturing the packets|Pcap.Net Tutorial - Opening an adapter and capturing the packets]]), but it uses ReceivePacket() instead of ReceivePackets().

The callback-based capture mechanism of ReceivePackets() is elegant and it could be a good choice in some situations. However, handling a callback is sometimes not practical -- it often makes the program more complex especially in situations with multithreaded applications.

In these cases, ReceivePacket() retrieves a packet with a direct call -- using ReceivePacket() packets are received only when the programmer wants them.

The parameter of this function is an output parameter for the capture packet.

In the following program, we recycle the callback code of the previous lesson's example and move it inside main() right after the call to ReceivePacket().

```C#
using System;
using System.Collections.Generic;
using PcapDotNet.Core;
using PcapDotNet.Packets;

namespace CapturingThePacketsWithoutTheCallback
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

                // Retrieve the packets
                Packet packet;
                do
                {
                    PacketCommunicatorReceiveResult result = communicator.ReceivePacket(out packet);
                    switch (result)
                    {
                        case PacketCommunicatorReceiveResult.Timeout:
                            // Timeout elapsed
                            continue;
                        case PacketCommunicatorReceiveResult.Ok:
                            Console.WriteLine(packet.Timestamp.ToString("yyyy-MM-dd hh:mm:ss.fff") + " length:" +
                                              packet.Length);
                            break;
                        default:
                            throw new InvalidOperationException("The result " + result + " shoudl never be reached here");
                    }
                } while (true);
            }
        }
    }
}
```

Notice also that ReceivePacket() returns different values for success, timeout elapsed, breakloop and EOF conditions.

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial - Opening an adapter and capturing the packets]] [[Next >>>|Pcap.Net Tutorial - Filtering the traffic]]