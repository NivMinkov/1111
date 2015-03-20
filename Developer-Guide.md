Here are the guidelines for **developing** Pcap.Net.  
If you want to **use** Pcap.Net in your own application, see the [[User Guide]].

## Checklist
The following items are needed before you can develop Pcap.Net:
* Microsoft Visual Studio 2010 Ultimate. <https://www.visualstudio.com/>
* Resharper 7.0. <http://www.jetbrains.com/resharper>
* WinPcap 4.1.3. <http://www.winpcap.org>
* Wireshark 1.12.3. <http://www.wireshark.org>
  * [x32 Downloads page](http://www.wireshark.org/download/win32/all-versions/)
  * [x64 Download page](http://www.wireshark.org/download/win64/all-versions/)
* NuGet Package Explorer <http://docs.nuget.org/docs/creating-packages/Using-a-GUI-to-build-packages>

## Compiling the code
* Make sure you have the checklist.
* Download the code (using the Source Code page or by connecting to the Source Control - if you got permissions).
* Build PcapDotNet.sln by going over the following steps:
  * Download WinPcap Developer's Pack (of the correct WinPcap version) and put it in the Source Code 3rdParty folder (so you'll have ...\PcapDotNet\3rdParty\WpdPack\... folders).
  * Open the PcapDotNet.sln and build it.
* Run all the unit tests of PcapDotNet.sln
* *Problems*:
  * If compilation fails - try to find the problem in the Discussions page or create a new discussion.
  * If tests fail - try to find the problem in the Discussions page or create a new discussion.

## Coding Conventions
* Warning Level: 4.
* The Resharper configuration file in the solution should be used.
* Code Analysis should run smoothly and the following rules exceptions are allowed:
  * CA1004 (Design): Generic methods should provide type parameter
  * CA1021 (Design): Avoid out parameters
  * CA1027 (Design): Mark enums with FlagsAttribute
  * CA1028 (Design): Enum storage should be Int32
  * CA1045 (Design): Do not pass types by reference
  * CA1056 (Design): URI properties should not be strings
  * CA1501 (Maintainability): Avoid excessive inheritance
  * CA1710 (Naming): Identifiers should have correct suffix
  * CA2000 (Reliability): Dispose objects before losing scope (perhaps this rule should be enabled after it is fixed in Visual Studio 2010).
  * CA2004 (Reliability): Remove calls to GC.KeepAlive
* Special cases to suppress Code Analysis warnings (case by case):
  * CA1025 (Design): Replace repetitive arguments with params array. *Can be suppressed when it makes sense to have more than 3 parameters of the same type but not unlimited number of parameters*
  * CA2227 (Usage): Collection properties should be read only - *Can be suppressed only if the Collection is a ReadOnlyCollection&lt;T>*

## Policies
* Every check-in *must* be commented.
* Solution *must* compile without errors or warnings.
* All unit tests *must* pass.
* Code coverage should be high (90%+).
* Everything public should be documented (you should get a warning for anything that should be documented but isn't).
* Work items of significant changes should be created. Significant changes are ones that users can notice (and so should be able to vote for).

## Building the documentation
* Build the entire PcapDotNet solution in release.
* Download and install Sandcastle Help File Builder 1.9.6.0 using SandcastleInstaller.exe. <http://shfb.codeplex.com>
* Open Pcap.Net Sandcastle Help File Builder project file located in the doc folder in the source control.
* Make sure the version is up-to-date. If not, update it (remember to check out the file and submit it afterwards).
* Build using Sandcastle Help File Builder.

## Releasing a new version
* Pre-release checklist:
  * Make sure the code compiles with a few warnings as possible (in release).
  * Make sure the code coverage is high.
  * Make sure the code metrics look good.
  * Build the documentation and make sure it looks good.
  * Make sure developer's pack solution compiles with *updated 3rdParty binaries*.
* Versioning:
  * Decide on the new version number.
  * PcapDotNet - change the version in all the AssemblyInfo.cs files and in the AssemblyInfo.cpp file.
  * PcapDotNet.DevelopersPack - change the version in all the AssemblyInfo.cs files.
  * Documentation - change the version in the PcapDotNet.shfbproj file (requires manual checkout).
  * Checkin the 22 files (note the new Change Set number).
* Folders:
  * PcapDotNet.Source.&lt;Version>.&lt;Change Set>
  * PcapDotNet.Binaries.&lt;Version>.&lt;Change Set> containing two folders:
    * PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>.x64
    * PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>.x86
  * PcapDotNet.Documentation.&lt;Version>.&lt;Change Set>
  * PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set> containing two folders:
    * PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x64 containing doc folder.
    * PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x86 containing doc folder.
* Build:
  * Close any solution opened.
  * Do the following twice, once for x86 and once for x64:
    * Delete the bin folder of PcapDotNet.
    * Build PcapDotNet in release according to the Compiling the code section above.
    * Copy the 12 dll, xml and pdb files (not the Test, TestUtils, CodeAnalysisLog.xml or lastcodeanalysissucceeded files) from the ...\PcapDotNet\bin\&lt;Architecture>\Release\ folder to the previously created PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>.&lt;Architecture> folder.
    * Note: only the Core files should be different between the two architectures.
* Download the Change Set source code and put its contents in the previously created PcapDotNet.Source.&lt;Version>.&lt;Change Set> folder.
* Create documentation and copy it to the previously created PcapDotNet.Documentation.&lt;Version>.&lt;Change Set> folder.
* Create Developer's pack:
  * Update the user guide with new versions of examples.
  * Copy the files from PcapDotNet.Source.&lt;Version>.&lt;Change Set> folder to the Developer's Pack folders (PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x64, PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x86).
  * Copy the files from PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>.x64 folder to PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x64\PcapDotNet.DevelopersPack\3rdParty\PcapDotNet folder.
  * Copy the files from PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>.x86 folder to PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x86\PcapDotNet.DevelopersPack\3rdParty\PcapDotNet folder.
  * Copy the files from PcapDotNet.Documentation.&lt;Version>.&lt;Change Set> to both Developer's Pack doc folders (PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x64\doc, PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>.x86\doc).
  * Delete all of the .vspscc and .vssscc files in the different folders under PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set> folder.
* Copy the license.txt file to PcapDotNet.Documentation.&lt;Version>.&lt;Change Set>, PcapDotNet.Binaries.&lt;Version>.&lt;Change Set> and PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set> folders.
* Zip each folder (PcapDotNet.Source.&lt;Version>.&lt;Change Set>, PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>, PcapDotNet.Documentation.&lt;Version>.&lt;Change Set>, PcapDotNet.DevelopersPack.&lt;Version>.&lt;Change Set>) to create 4 matching zip files.
* Create a new release:
  * Named it Pcap.Net &lt;Version> (&lt;Change Set>).
  * Copy the description from a previous release and change the necessary sections (use the comments on the different Change Sets to describe the changes).
  * The files should be ordered Developer's Pack, Binaries, Documentation, Source.
  * Developer's Pack is the recommended download.
* Update NuGet packages. For each architecture (x64 and x86) do the following:
  * Open Pcap.Net.&lt;Architecture>.nupkg.
  * Edit Metadata: Update Version and Release notes.
  * Under lib, remove the net40 directory.
  * Add net40 directory.
  * Add the binaries from the matching PcapDotNet.Binaries.&lt;Version>.&lt;Change Set>.&lt;Architecture> directory.
  * Save.
  * Go to https://www.nuget.org.
  * Upload the Package.

## Bugs in other products that were found during the development of Pcap.Net *and were fixed*:
### Windows Bugs
* Time Zones bug (Reported May 14th, 2010): https://connect.microsoft.com/VisualStudio/feedback/details/559198/net-4-datetime-tolocaltime-is-sometimes-wrong (Microsoft deleted that page).

### WinPcap Bugs
* WinPcap's driver bug that causes BSOD in Windows (Reported November 12th, 2009): <http://www.winpcap.org/pipermail/winpcap-bugs/2009-November/001094.html>, <http://www.winpcap.org/misc/changelog.htm>

### Wireshark Bugs
* ICMP Redirect takes 4 bytes for IPv4 payload instead of 8 (Reported February 21st, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10992>
* Length of original datagram in ICMP Parameter Problem message is treated as the total IPv4 length (Reported February 21st, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10991>
* IPv6 Local Mobility Anchor Address mobility option code is treated incorrectly (Reported February 14th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10961>
* DNS LOC Precision missing units (Reported February 7th, 2015): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10940>
* HTTP chunked response includes data beyond the chunked response (Reported November 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10707>
* NLPID not shown for protocols where the NLPID is not part of the PDU (Reported November 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10706>
* IPv6 Experimental mobility header data is interpreted as options (Reported November 14th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10703>
* DNS NAPTR RR Replacement Length is incorrect (Reported November 14th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10700>
* IPv6 Experimental mobility option includes too many bytes for the Data field (Reported November 8th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10682>
* IPv6 Mobility Option Context Request reads an extra request (Reported November 7th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10676>
* DNS WKS RR Protocol field is read as 4 bytes instead of 1 (Reported November 7th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10675>
* DNS Name Length for Zone RR on root is 6 and Label Count is 1 (Reported November 7th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10674>
* IPv6 CGA Parameters mobility option includes too many bytes for CGA Parameters field (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10654>
* IPv6 Signature mobility option includes too many bytes for CGA Parameters field (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10653>
* DNS A6 Address Suffix field is parsed incorrectly (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10652>
* TShark crashes when running with PDML on a specific packet (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10651>
* DNS ISDN RR Sub Address field is read one byte early (Reported November 1st, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10650>
* IPv6 Local Mobility Anchor Address mobility option's code and reserved fields are parsed as 2 bytes instead of 1 (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10630>
* IPv6 DNS-UPDATE-TYPE mobility option includes too many bytes for the MD identity field (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10629>
* IPv6 Mobility Header Link-Layer Address Mobility Option is parsed incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10627>
* IPv6 AUTH mobility option parses Mobility SPI and Authentication Data incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10626>
* IPv6 MESG-ID mobility option is parsed incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10625>
* IPv6 Care Of Test mobility option includes too many bytes for the Keygen Token field (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10624>
* IPv6 Redirect Mobility Option K and N bits are parsed incorrectly (Reported October 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10622>
* IPv6 Permanent Home Keygen Token mobility option includes too many bytes for the token field (Reported October 24th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10619>
* IPv6 Vendor Specific Mobility Option includes the next mobility option type (Reported October 24th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10618>
* DNS NXT RR is parsed incorrectly (Reported October 24th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10615>
* IPv6 Mobility Option Mobile Node Link Layer Identifier Link-layer Identifier field is read beyond the option data (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10578>
* IPv6 Mobility Option Binding Authorization Data for FMIPv6 Authenticator field is read beyond the option data (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10577>
* IPv6 Mobility Option IPv6 Address/Prefix marks too many bytes for the address/prefix field (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10576>
* IPv6 QuickStart option Nonce is read incorrectly (Reported October 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10575>
* IPv6 Calipso option Compartment Length is calculated incorrectly (Reported October 11th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10561>
* IPv6 RPL option is read as less bytes than it is (Reported October 11th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10559>
* IPv4 Protocols names: IDPR and IDRP (Reported May 16th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10110>
* IPv6 Mobility Option Service Selection with option length = 1 is considered invalid (Reported April 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10045>
* IPv6 Mobility Option Link Layer Address parses 0 length address as if it is of length 1 (Reported April 25th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10043>
* IPv6 Mobility Option Binding Revocation recognize second flag as Acknowledge (Reported April 21th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10015>
* IPv6 Mobility Header Binding Revocation Acknowledge Global field is the wrong bit (Reported April 18th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10007>
* IPv6 Mobility Header Link Layer Address is parsed incorrectly (Reported April 18th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10006>
* IPv6 Authentication Header not parsed after Mobility Header (Reported April 18th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=10005>
* IPv6 Next Header 0x3d recognized as SHIM6 (Reported April 15th, 2014): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=9995>
* The basic security IP option is implemented according to RFC 791 instead of RFC 1108 (Reported April 10th, 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=7064>
* DNS OPT flags are not parsed correctly (Reported April 8th, 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=7045>
* IPv4 destination is calculated incorrectly when there are bad options before source routing options (Reported April 8th, 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=7043>
* DNS KEY has an extra "Key id" field (Reported February 6th , 2012): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=6704>
* IPv4 checksum says "not all data available" when all data is available (Reported September 9th, 2010): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=5194>
* HTTP empty URIs (Reported September 5th, 2010): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=5181>
* Packet causes Wireshark to crash (Reported August 21st, 2009): <https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=3925>