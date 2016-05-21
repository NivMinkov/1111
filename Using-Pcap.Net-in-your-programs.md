## Using Pcap.Net in your programs

### Creating an application that uses Pcap.Net

To create an application that uses Pcap.Net with Microsoft Visual Studio, follow these steps:
* Make sure you have .NET Framework 4.5 (or a newer version) installed.
  * [Download Microsoft .NET Framework 4.5](https://www.microsoft.com/en-us/download/details.aspx?id=30653)
  * If you want to use .NET Framework 4, you can only use Pcap.Net 1.0.3.
  * If you want to use .NET Framework 3.5 SP1, you can only use Pcap.Net 0.6.0.
* Add a reference to PcapDotNet.Base.dll, PcapDotNet.Packets.dll, PcapDotNet.Core.dll and PcapDotNet.Core.Extensions.dll at every project file that uses Pcap.Net.
  * [How to: Add or Remove References in Visual Studio](https://msdn.microsoft.com/en-us/library/wkze6zky(v=vs.100).aspx)

Look at [[Pcap.Net Tutorial]] for sample programs.

### Running Pcap.Net applications

To run an application that uses Pcap.Net, follow these steps:
* Make sure the application you're using was built in Release mode and not Debug.
* Install WinPcap 4.1.3 (available at <http://www.winpcap.org>).
* Install .NET Framework 4.0 (available at [Microsoft Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=17718)).
* Install Microsoft Visual C++ 2010 Redistributable Package (available at Microsoft Download Center: [x86 version](http://www.microsoft.com/en-us/download/details.aspx?id=5555), [x64 version](http://www.microsoft.com/en-us/download/details.aspx?id=14632)). If you have troubles on Windows x64 versions, try installing the x86 version of the Microsoft Visual C++ 2010 Redistributable Package.