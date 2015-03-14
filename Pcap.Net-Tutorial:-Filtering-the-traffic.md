## Filtering the traffic

One of the most powerful features offered by Pcap.Net (and by WinPcap and libpcap as well) is the filtering engine. It provides a very efficient way to receive subsets of the network traffic, and is (usually) integrated with the capture mechanism provided by Pcap.Net. The functions used to filter packets are CreateFilter() and SetFilter().

CreateFilter() takes a string containing a high-level Boolean (filter) expression and produces a low-level byte code that can be interpreted by the filter engine in the packet driver. The syntax of the boolean expression can be found in the [WinPcap Filtering expression syntax](https://www.winpcap.org/docs/docs_412/html/group__language.html) section.

SetFilter() associates a filter with a capture session in the kernel driver. Once SetFilter() is called, the associated filter will be applied to all the packets coming from the network, and all the conformant packets (i.e., packets for which the Boolean expression evaluates to true) will be actually copied to the application.

The following code shows how to compile and set a filter. Note that the Device's netmask is used, because some filters created by CreateFilter() require it.

The filter passed to CreateFilter() in this code snippet is "ip and tcp", which means to "keep only the packets that are both IPv4 and TCP and deliver them to the application".

```C#
// Compile the filter
using (BerkeleyPacketFilter filter = communicator.CreateFilter("ip and tcp"))
{
    // Set the filter
    communicator.SetFilter(filter);
}
```

The code above can be replaced in this simple case with a simple SetFilter() call.
```C#
// Compile and set the filter
communicator.SetFilter("ip and tcp");
```

If you want to see some code that uses the filtering functions shown in this lesson, look at the example presented in the next Lesson, [[Interpreting the packets|Pcap.Net Tutorial: Interpreting the packets]].

[[&lt;&lt;&lt; Previous|Pcap.Net Tutorial: Capturing the packets without the callback]] [[Next >>>|Pcap.Net Tutorial: Interpreting the packets]]