## Gathering Statistics on the network traffic

This lesson shows another advanced feature of Pcap.Net: the ability to collect statistics about network traffic. The statistical engine makes use of the kernel-level packet filter to efficiently classify the incoming packet. You can take a look at the NPF driver internals manual if you want to know more details.

In order to use this feature, the programmer must open an adapter and put it in statistical mode. This can be done with the Mode property. In particular, Mode.Statistics must be used as the mode value of this property.

With statistical mode, making an application that monitors the TCP traffic load is a matter of few lines of code. The following sample shows how to do it.

```C#
using System;
using System.Collections.Generic;
using PcapDotNet.Core;

namespace GatheringStatisticsOnTheNetworkTraffic
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
            PacketDevice selectedOutputDevice = allDevices[deviceIndex - 1];

            // Open the output adapter
            using (
                PacketCommunicator communicator = selectedOutputDevice.Open(100, PacketDeviceOpenAttributes.Promiscuous,
                                                                            1000))
            {
                // Compile and set the filter
                communicator.SetFilter("tcp");

                // Put the interface in statstics mode
                communicator.Mode = PacketCommunicatorMode.Statistics;

                Console.WriteLine("TCP traffic summary:");

                // Start the main loop
                communicator.ReceiveStatistics(0, StatisticsHandler);
            }
        }

        private static void StatisticsHandler(PacketSampleStatistics statistics)
        {
            // Current sample time
            DateTime currentTimestamp = statistics.Timestamp;

            // Previous sample time
            DateTime previousTimestamp = _lastTimestamp;

            // Set _lastTimestamp for the next iteration
            _lastTimestamp = currentTimestamp;

            // If there wasn't a previous sample than skip this iteration (it's the first iteration)
            if (previousTimestamp == DateTime.MinValue)
                return;

            // Calculate the delay from the last sample
            double delayInSeconds = (currentTimestamp - previousTimestamp).TotalSeconds;

            // Calculate bits per second
            double bitsPerSecond = statistics.AcceptedBytes * 8 / delayInSeconds;

            // Calculate packets per second
            double packetsPerSecond = statistics.AcceptedPackets / delayInSeconds;

            // Print timestamp and samples
            Console.WriteLine(statistics.Timestamp + " BPS: " + bitsPerSecond + " PPS: " + packetsPerSecond);
        }

        private static DateTime _lastTimestamp;
    }
}
```

Before enabling statistical mode, the user has the option to set a filter that defines the subset of network traffic that will be monitored. See the paragraph on the [url:WinPcap Filtering expression syntax|https://www.winpcap.org/docs/docs_40_2/html/group__language.html] for details. If no filter has been set, all of the traffic will be monitored.

Once
* The filter is set
* PacketCommunicator.Mode is set
* Callback invocation is enabled with ReceiveStatistics()

The interface descriptor starts to work in statistical mode. Notice the third parameter (readTimeout) of Open(): it defines the interval among the statistical samples. The callback function receives the samples calculated by the driver every readTimeout milliseconds.

In the example, the adapter is opened with a timeout of 1000 ms. This means that StatisticsHandler() is called once per second. At this point a filter that keeps only tcp packets is compiled and set. Then the Mode property is set and ReceiveStatistics() is called. Note that there's a static data member, _lastTimestamp, which is used to store the previous timestamp in order to calculate the interval between two samples. StatisticsHandler()uses this interval to obtain the bits per second and the packets per second and then prints these values on the screen.

Note finally that this example is by far more efficient than a program that captures the packets in the traditional way and calculates statistics at user-level. Statistical mode requires the minumum amount of data copies and context switches and therefore the CPU is optimized. Moreover, a very small amount of memory is required.

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial: Sending Packets]] [[Next >>>|Pcap.Net Tutorial: Using LINQ]]