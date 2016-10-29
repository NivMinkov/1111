Here are the guidelines for **developing** Pcap.Net.  
If you want to **use** Pcap.Net in your own application, see the [[User Guide]].

## Checklist
The following items are needed before you can develop Pcap.Net:
* Microsoft Visual Studio Ultimate 2013. <https://www.visualstudio.com/>
* ReSharper 9.0. <http://www.jetbrains.com/resharper>
* WinPcap 4.1.3. <http://www.winpcap.org>
* Wireshark 1.12.8. <http://www.wireshark.org>
  * [x32 Downloads page](http://www.wireshark.org/download/win32/all-versions/)
  * [x64 Download page](http://www.wireshark.org/download/win64/all-versions/)
* NuGet Package Explorer <http://docs.nuget.org/docs/creating-packages/Using-a-GUI-to-build-packages>

## Compiling the code
* Make sure you have the checklist.
* Download the code (by clicking the "Download ZIP" button or by cloning the repository).
* Build PcapDotNet.sln by going over the following steps:
  * Download WinPcap Developer's Pack (of the correct WinPcap version) and put it in the Source Code 3rdParty folder (so you'll have ...\PcapDotNet\3rdParty\WpdPack\... folders).
  * Open the PcapDotNet.sln and build it.
* Run all the unit tests of PcapDotNet.sln
* **Problems**:
  * If compilation fails - try to find the problem in the Q&A group or create a new thread.
  * If tests fail - try to find the problem in the Q&A group  page or create a new thread.

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
  * CA1025 (Design): Replace repetitive arguments with params array. **Can be suppressed when it makes sense to have more than 3 parameters of the same type but not unlimited number of parameters.**
  * CA2227 (Usage): Collection properties should be read only - **Can be suppressed only if the Collection is a ReadOnlyCollection&lt;T>**.
* For C++ CLI projects, the following rules exceptions are also allowed:
  * CA2151 (Security): Fields with critical types should be security critical.

## Policies
* Every commit **must** be commented.
* Solution **must** compile without errors or warnings.
* All unit tests **must** pass.
* Code coverage should be high (90%+).
* Everything public should be documented (you should get a warning for anything that should be documented but isn't).
* Work items of significant changes should be created. Significant changes are ones that users can notice (and so should be able to vote for).
* Report bugs found in other products and update [[this list|Bugs in other products that were found during the development of Pcap.Net and were fixed]].

## Building the documentation
* Build the entire PcapDotNet solution in release.
* Download and install Sandcastle Help File Builder 1.9.6.0 using SandcastleInstaller.exe. <http://shfb.codeplex.com>
* Open Pcap.Net Sandcastle Help File Builder project file located in the doc folder in the source control.
* Make sure the version is up-to-date. Remember to commit it afterwards.
* Build using Sandcastle Help File Builder.

## Releasing a new version
* Pre-release checklist:
  * Make sure the code compiles with a few warnings as possible (in release).
  * Make sure the code coverage is high.
  * Make sure the code metrics look good.
  * Build the documentation and make sure it looks good.
  * Make sure developer's pack solution compiles with **updated 3rdParty binaries**.
* Versioning:
  * Decide on the new version number.
  * PcapDotNet - change the version in all the AssemblyInfo.cs files and in the AssemblyInfo.cpp file.
  * PcapDotNet.DevelopersPack - change the version in all the AssemblyInfo.cs files.
  * Documentation - change the version in the PcapDotNet.shfbproj file.
  * Commit the 22 files.
* Folders:
  * PcapDotNet.Source.&lt;Version>
  * PcapDotNet.Binaries.&lt;Version> containing two folders:
    * PcapDotNet.Binaries.&lt;Version>.x64
    * PcapDotNet.Binaries.&lt;Version>.x86
  * PcapDotNet.Documentation.&lt;Version>
  * PcapDotNet.DevelopersPack.&lt;Version> containing two folders:
    * PcapDotNet.DevelopersPack.&lt;Version>.x64 containing doc folder.
    * PcapDotNet.DevelopersPack.&lt;Version>.x86 containing doc folder.
* Build:
  * Close any solution opened.
  * Do the following twice, once for x86 and once for x64:
    * Delete the bin folder of PcapDotNet.
    * Build PcapDotNet in release according to the Compiling the code section above.
    * Copy the 12 dll, xml and pdb files (not the Test, TestUtils, CodeAnalysisLog.xml or lastcodeanalysissucceeded files) from the ...\PcapDotNet\bin\\&lt;Architecture>\Release\ folder to the previously created PcapDotNet.Binaries.&lt;Version>.&lt;Architecture> folder.
    * Note: only the Core files should be different between the two architectures.
* [Download](https://github.com/PcapDotNet/Pcap.Net/archive/master.zip) the source code and put its contents in the previously created PcapDotNet.Source.&lt;Version> folder.
* Create documentation and copy it to the previously created PcapDotNet.Documentation.&lt;Version> folder.
* Create Developer's pack:
  * Update the user guide with new versions of examples.
  * Copy the files from PcapDotNet.Source.&lt;Version> folder to the Developer's Pack folders (PcapDotNet.DevelopersPack.&lt;Version>.x64, PcapDotNet.DevelopersPack.&lt;Version>.x86).
  * Copy the files from PcapDotNet.Binaries.&lt;Version>.x64 folder to PcapDotNet.DevelopersPack.&lt;Version>.x64\PcapDotNet.DevelopersPack\3rdParty\PcapDotNet folder.
  * Copy the files from PcapDotNet.Binaries.&lt;Version>.x86 folder to PcapDotNet.DevelopersPack.&lt;Version>.x86\PcapDotNet.DevelopersPack\3rdParty\PcapDotNet folder.
  * Copy the file from PcapDotNet.Documentation.&lt;Version> to both Developer's Pack doc folders (PcapDotNet.DevelopersPack.&lt;Version>.x64\doc, PcapDotNet.DevelopersPack.&lt;Version>.x86\doc).
  * Delete all of the .vspscc and .vssscc files in the different folders under PcapDotNet.DevelopersPack.&lt;Version> folder.
* Copy the license.txt file to PcapDotNet.Documentation.&lt;Version>, PcapDotNet.Binaries.&lt;Version> and PcapDotNet.DevelopersPack.&lt;Version> folders.
* Zip each folder (PcapDotNet.Source.&lt;Version>, PcapDotNet.Binaries.&lt;Version>, PcapDotNet.Documentation.&lt;Version>, PcapDotNet.DevelopersPack.&lt;Version>) to create 4 matching zip files.
* Create a new release:
  * Named it Pcap.Net &lt;Version>.
  * Copy the description from a previous release and change the necessary sections (use the comments on the different commits to describe the changes).
  * The files should be ordered Developer's Pack, Binaries, Documentation, Source.
  * Developer's Pack is the recommended download.
* Update NuGet packages. For each architecture (x64 and x86) do the following:
  * Open Pcap.Net.&lt;Architecture>.nupkg.
  * Edit Metadata: Update Version and Release notes.
  * Under lib, remove the net45 directory.
  * Add net45 directory.
  * Add the binaries from the matching PcapDotNet.Binaries.&lt;Version>.&lt;Architecture> directory.
  * Save.
  * Go to https://www.nuget.org.
  * Upload the Package.