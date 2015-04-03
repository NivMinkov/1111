## Handling offline dump files

In this lession we are going to learn how to handle packet capture to a file (dump to file). Pcap.Net offers a wide range of functions to save the network traffic to a file and to read the content of dumps -- this lesson will teach how to use all of these functions.

The format for dump files is the libpcap one. This format contains the data of the captured packets in binary form and is a standard used by many network tools including WinDump, Ethereal and Snort.

### Saving packets to a dump file

First of all, let's see how to write packets in libpcap format.

The following example captures the packets from the selected interface and saves them on a file whose name is provided by the user.

```C#
using System;
using System.Collections.Generic;
using PcapDotNet.Core;

namespace SavingPacketsToADumpFile
{
    class Program
    {
        static void Main(string[] args)
        {
            // Check command line
            if (args.Length != 1)
            {
                Console.WriteLine("usage: " + Environment.GetCommandLineArgs()[0] + " <filename>");
                return;
            }

            // Retrieve the device list on the local machine
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
                selectedDevice.Open(65536, // portion of the packet to capture
                                    // 65536 guarantees that the whole packet will be captured on all the link layers
                                    PacketDeviceOpenAttributes.Promiscuous, // promiscuous mode
                                    1000)) // read timeout
            {
                // Open the dump file
                using (PacketDumpFile dumpFile = communicator.OpenDump(args[0]))
                {
                    Console.WriteLine("Listening on " + selectedDevice.Description + "... Press Ctrl+C to stop...");

                    // start the capture
                    communicator.ReceivePackets(0, dumpFile.Dump);
                }
            }
        }
    }
}
```

As you can see, the structure of the program is very similar to the ones we have seen in the previous lessons. The differences are:
* A call to OpenDump() is issued once the interface is opened. This call opens a dump file and associates it with the interface.
* The packets are written to this file by setting the callback to the dump file's Dump() method. A different approach would be to give a specially created method for as a callback or an anonymous delegate.
* A different and very simple way to dump Packets into a file is by calling PacketDumpFile.Dump() static method.

### Reading packets from a dump file

Now that we have a dump file available, we can try to read its content. The following code opens a libpcap dump file and displays every packet contained in the file. The file is opened with Open(), then the usual ReceivePackets() is used to sequence through the packets. As you can see, reading packets from an offline capture is nearly identical to receiving them from a physical interface.

```C#
using System;
using PcapDotNet.Core;
using PcapDotNet.Packets;

namespace ReadingPacketsFromADumpFile
{
    class Program
    {
        static void Main(string[] args)
        {
            // Check command line
            if (args.Length != 1)
            {
                Console.WriteLine("usage: " + Environment.GetCommandLineArgs()[0] + " <filename>");
                return;
            }

            // Create the offline device
            OfflinePacketDevice selectedDevice = new OfflinePacketDevice(args[0]);

            // Open the capture file
            using (PacketCommunicator communicator =
                selectedDevice.Open(65536,                                  // portion of the packet to capture
                                                                            // 65536 guarantees that the whole packet will be captured on all the link layers
                                    PacketDeviceOpenAttributes.Promiscuous, // promiscuous mode
                                    1000))                                  // read timeout
            {
                // Read and dispatch packets until EOF is reached
                communicator.ReceivePackets(0, DispatcherHandler);
            }
        }

        private static void DispatcherHandler(Packet packet)
        {
            // print packet timestamp and packet length
            Console.WriteLine(packet.Timestamp.ToString("yyyy-MM-dd hh:mm:ss.fff") + " length:" + packet.Length);

            // Print the packet
            const int LineLength = 64;
            for (int i = 0; i != packet.Length; ++i)
            {
                Console.Write((packet[i]).ToString("X2"));
                if ((i + 1) % LineLength == 0)
                  Console.WriteLine();
            }

            Console.WriteLine();
            Console.WriteLine();
        }
    }
}
```

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial Interpreting the packets]] [[Next >>>|Pcap.Net Tutorial Sending Packets]]