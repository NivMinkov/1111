## Obtaining advanced information about installed devices

Lesson 1 ([[Obtaining the device list|Pcap.Net Tutorial: Obtaining the device list]]) demonstrated how to get basic information (i.e. device name and description) about available adapters. Actually, Pcap.Net provides also other advanced information. In particular, every LivePacketDevice instance returned by LivePacketDevice.AllLocalMachine contains also a list of DeviceAddress instances, with:
* The address for that interface.
* A netmask.
* A broadcast address.
* A destination address.

The following sample provides an DevicePrint() function that prints the complete contents of a LivePacketDevice instance. It is invoked by the program for every entry returned by LivePacketDevice.AllLocalMachine.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using PcapDotNet.Core;

namespace ObtainingAdvancedInformationAboutInstalledDevices
{
    class Program
    {
        static void Main(string[] args)
        {
            // Retrieve the interfaces list
            IList<LivePacketDevice> allDevices = LivePacketDevice.AllLocalMachine;

            // Scan the list printing every entry
            for (int i = 0; i != allDevices.Count(); ++i)
                DevicePrint(allDevices[i]);
        }

        // Print all the available information on the given interface
        private static void DevicePrint(IPacketDevice device)
        {
            // Name
            Console.WriteLine(device.Name);

            // Description
            if (device.Description != null)
                Console.WriteLine("\tDescription: " + device.Description);

            // Loopback Address
            Console.WriteLine("\tLoopback: " +
                              (((device.Attributes & DeviceAttributes.Loopback) == DeviceAttributes.Loopback)
                                   ? "yes"
                                   : "no"));

            // IP addresses
            foreach (DeviceAddress address in device.Addresses)
            {
                Console.WriteLine("\tAddress Family: " + address.Address.Family);

                if (address.Address != null)
                    Console.WriteLine(("\tAddress: " + address.Address));
                if (address.Netmask != null)
                    Console.WriteLine(("\tNetmask: " + address.Netmask));
                if (address.Broadcast != null)
                    Console.WriteLine(("\tBroadcast Address: " + address.Broadcast));
                if (address.Destination != null)
                    Console.WriteLine(("\tDestination Address: " + address.Destination));
            }
            Console.WriteLine();
        }
    }
}
```

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial: Obtaining the device list]] [[Next >>>|Pcap.Net Tutorial: Opening an adapter and capturing the packets]]