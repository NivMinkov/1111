## Obtaining the device list

Typically, the first thing that a Pcap.Net-based application does is get a list of attached network adapters. Pcap.Net provide the LivePacketDevice.AllLocalMachine static property for this purpose: this property returns a collection of LivePacketDevice instances, each of which contains comprehensive information about an attached adapter. In particular, the fields name and description contain the name and a human readable description, respectively, of the corresponding device.

The following code retrieves the adapter list and shows it on the screen, printing an error if no adapters are found.

```C#
using System;
using System.Collections.Generic;
using PcapDotNet.Core;

namespace ObtainingTheDeviceList
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
        }
    }
}
```

Some comments about this code.

First of all, LivePacketDevice.AllLocalMachine, like other Pcap.Net methods, can throw an exception with a description of the error if something goes wrong.

Second, remember that not all the OSes supported by libpcap provide a description of the network interfaces, therefore if we want to write a portable application, we must consider the case in which description is null: we print the string "No description available" in that situation.

Let's try to compile and run the code of this first sample.

On Windows, you will need to create a project, following the instructions in the [Using Pcap.Net in your programs] section. However, we suggest that you use the Pcap.Net developer's pack, since it provides many examples already configured as projects including all the code presented in this tutorial and the dlls needed to compile and run the examples.

Assuming we have compiled the program, let's try to run it. On a particular WinXP workstation, the result we obtained is
```
1. rpcap://\Device\NPF_{555EA70B-BC91-4520-8307-E7FC043C6997} (Network adapter 'Atheros AR8121/AR8113 PCI-E Ethernet Controller (Microsoft's Packet Scheduler) ' on local host)
```
As you can see, the name of the network adapters (that will be passed to libpcap when opening the devices) under Windows are quite unreadable, so the parenthetical descriptions can be very helpful.

[[<<< Previous|Pcap.Net Tutorial]] [[Next >>>|Pcap.Net Tutorial: Obtaining advanced information about installed devices]]