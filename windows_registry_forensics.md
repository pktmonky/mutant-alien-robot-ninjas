# Intro
## Locations of Windows Registry Hives:
* Core system registry hive files (SAM, Security, Software, System) - c:\Windows\system32\config
* (Windows 7) Components - c:\Windows\system32\config
* (Windows 8) BBI, BCD, Components, Drivers, ELAM - c:\Windows\system32\config
* (Fully patched Windows 7 and above) AmCache - c:\Windows\AppCompat\Programs
* NTUSER.DAT - c:\Users | "c:\Documents and Settings"

## Past Work:
* Peter Nordahl's offline NT Password and Registry Editor [ntpasswd](http://pogostick.net/%7Epnh/ntpasswd)
* [Parse::Win32Registry](https://code.google.com/p/parse-win32registry/)
* Jolanta Thomassen's [graduate thesis](http://sentinelchicken.com/data/JolantaThomassenDISSERTATION.pdf) and resulting tool, regslack
* Peter Norris's master's thesis, [The Internal Structure of the Windows Registry](http://amnesia.gtisc.gatech.edu/%7Emoyix/suzibandit.ltd.uk/MSc/)

## Useful Links: 
* [Yogesh Khatri's Blog post on AmCache.hve](http://www.swiftforensics.com/2013/12/amcachehve-in-windows-8-goldmine-for.html)
* [Corey Harrell's Blog post on Exploring the Application Experience and Compatibility feature](http://journeyintoir.blogspot.in/2013/12/revealing-recentfilecachebcf-file.html)
* [Alex Ionescu's Blog post on Secrets of the Application Compatilibity Database](http://www.alex-ionescu.com/?p=39)
* [Mark Russinovich’s “Inside the Registry” article in Windows NT Magazine](http://technet.microsoft.com/en-us/library/cc750583.aspx)

# Processes and Tools
## Viewers
* Regedit - OS provided; be careful, the last key in focus when the application is closed is recorded in the user's NTUSER.DAT hive
* MiTeC Windows Registry Recovery (WRR) - Familiar interface; Common and Explorer tasks are provided; good search capability; doesn't handle ShimCache or AppCompatCache data
* Registry Explorer - clean and simple interface for live and offline viewing

## Parsers
* SysInternals Autoruns
* Didier Stevens's UserAssist parses the UserAssist data within the user's NTUSER.DAT hive file. Data includes information about applications a user executed from the Desktop, from a Windows Explorer folder, or from the Program Menu
* Mandiant's ShimCAcheParser parses the AppCompatCache value data, part of the Application Compatibility functionality of Windows
* Willi Ballenthin's amcache.py script parses the AmCache hive file
* Harlan Carvey's Regripper which contains tools to achieve all of the above functionality and more

## Timeline Analysis
* regtime extracts the key names, paths, and LastWrite times for the given hive file and prints the results to STDOUT; command line option to prepend a string to each key path
* _tln plug-ins for RegRipper extract timestamped information from various registry value data and presents in five-field TLN format

## Diffing
* Regshot is very useful for malware analysis
* Parse::Win32 contains a tool regdiff.pl which does the same as Regshot but could also work for forensic analysis with a baseline registry hive file

## Deleted Keys and Values
* regslack is a perl script to detect and recover deleted keys and values from a hive file
* Parse::Win32 contains a tool regscan.pl which could be modified to only display unallocated (deleted) keys and values from a hive file

## Userful Links:
* [Didier Stevens' UserAssist tool](http://blog.didierstevens.com/programs/userassist/)
* [Mandiant's ShimCacheParser tool](https://github.com/mandiant/ShimCacheParser) and [paper](https://dl.mandiant.com/EE/library/Whitepaper_ShimCacheParser.pdf) on the tools purpose 
* [Eric Zimmerman's Blog Binary Foray](https://binaryforay.blogspot.com/) and developed tools, [ShellBag Explorer, Registry Explorer, and AmCacheParser](https://ericzimmerman.github.io/)
* [Willi Ballenthin's amcache.py script](https://github.com/williballenthin/python-registry/blob/master/samples/amcache.py)
* [Harlan Carvey's RegRipper](https://github.com/keydet89/RegRipper2.8)

# Analyzing the System Hives
## Intro
Keys and values are grouped by artifact categories developed by [Corey Harrell](http://journeyintoir.blogspot.com) and presented on the [2012 SANS DFIR poster](http://digital-forensics.sans.org/blog/2012/06/18/sans-digital-forensics-and-incident-response-poster-released)

## Security Hive
Mostly pertains to the system and not specific users on the system. Also contains on special case within the "program execution" category
* HKLM\Security\Policy\Secrets - LastWrite time of the this key is modified when GSecDump is used to dump the LSA secrets from the system
* HKLM\SECURITY\Policy\PolAcDmS - "Default" value within this key contains the SID of the system and can be used to differentiate local users from domain users
* HKLM\SECURITY\Policy\PolPrDms - "Default" value within this key contains the SID of the primary domain for the system
* HKLM\SECURITY\Policy\PolAdtEv - This key contains the auditing policy for the system. This information can be seen on a live system within the Local Security Policy applet or using the autidpol command line tool

## SAM Hive
* HKLM\SAM\Domains\Account\Users\<RID> - Each account contains two values: F (time stamps) and V (user account information) and are both binary data types

## System Hive
### System Configuration Artifacts
* The CurrentControlSet is a volatile portion of the Registry hive. The Select key within the CurrentControlSet lists the ControlSet loaded last time the system was running
* HKLM\SYSTEM\CurrentControlSet\Control\ComputerName\ActiveComputerName - System name
* HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management - ClearPagefileAtShutdown value within this key determines whether the page file is cleared when the system is shut down
* HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces - contains subkeys of network interfaces by GUID, containing detailed information like IP Address and DHCP leases
* HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\PersistentRoutes - contains persistent routes, sometimes used to null route AV and SW update services
* HKLM\SYSTEM\CurrentControlSet\FileSystem - contains configuration information about the file system, including a value NtfsDisableLastAccessUpdate which disables the recording of Last Access Times on all files
* HKLM\SYSTEM\CurrentControlSet\Control\Session Management\Memory Management\PrefetchParameters - contains configuration information about the application prefectching, including the value EnablePrefetcher which disables application prefetching

### AutoStart Artifacts
* HKLM\SYSTEM\CurrentControlSet\Services\<service-name> - contains subkeys and values pertaining to Windows Services; the Start value details how a service is started
* HKLM\SYSTEM\CurrentControlSet\Enum\Root - contains subkeys logging when a service was originally executed and an additional subkey, 000, logging the last time the service was executed

### Program Execution Artifacts
* HKLM\SYSTEM\CurrentControlSet\Session Manager\AppCompatCache - contains the ShimCache in the AppCompatCache value under this key

### Malware Artifacts
Techniques used by malware: right-to-left encoding to hide a malicious service with a trusted name; Autostart registry contains a script which decodes and executes the malware stored in another registry key/value

### USB
* HKLM\SYSTEM\CurrentControlSet\Enum\USBStor - contains detailed information and time stamps about connected USB storage devices
* HKLM\SYSTEM\CurrentControlSet\Enum\USB - identical to above

### Useful Links
* [How to Hide Malware in Unicode](http://www.secureworks.com/resources/blog/how-to-hide-malware-in-unicode)

## Software Hive
Note: Registry redirection is used by 64-bit Windows OS to handle the difference between 32-bit and 64-bit software.

### System Configuration Artifacts
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion - contains values describing the Operating System (build number, version, patch level)
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList - contains subkeys for each user profile on the system, which contain values describing each user profile
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkCards - contains subkeys for each interface that has been connected to the system, each containing values describing the network interface
* HKLM\SOFTWARE\Microsoft\WZCSVC\Parameters\Interfaces - contains subkeys for each wireless interface that has been connected to the system, each containing values describing the wireless interface including a value for each WAP that the interface has connected.
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles - same as above and more on Windows Vista and newer

### AutoStart Artifacts
* HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run - most prominent autostart location
* HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run - similar to above
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify - contains values which include packages (DLLs) that receive notifications from the WinLogon process.
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options - subkeys mapping a custom debugger to a process
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows - contains a value "AppInit_DLLs" which defines a list of DLLs that are loaded by every GUI application
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Shell Extensions\Approved - contains GUIDs which map to approved DLL paths loaded when a GUI application imports a DLL
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Explorer\Browser Helper Objects - contains DLLs loaded by Internet Explorer for providing additional functionality, i.e. plug-ins
* HKLM\SOFTWARE\Microsoft\Windows NT\Schedule\TaskCache\Tree - contains scheduled tasks
* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\AppCompatFlags - contain Application Compatibility Database

### Malware Artifacts
Malware can each use novel ways of hiding under other programs or devices

### Useful LInks
* [Using Image File Execution option as an Attack Vector on Windows](http://silverstr.ufies.org/blog/archives/000809.html)
* [Using and Abusing Microsoft's Fix It Patches](https://www.blackhat.com/docs/asia-14/materials/Erickson/WP-Asia-14-Erickson-Persist-It-Using-And-Abusing-Microsofts-Fix-It-Patches.pdf)
* [How EMET Works](http://0xdabbad00.com/2010/09/12/how-emet-works/)
* [Forensic Analysis of Sound Mixer](http://www.ghettoforensics.com/2014/11/dj-forensics-analysis-of-sound-mixer.html)

## AmCache Hive
* Files - contains subkeys mapping to file references and last modified time stamps
* Programs - contains subkeys mapping to programs similar to the "Programs and Features" applet and each contains data values describing the program, including files included in MSI packages

[AmCache Hve in Windows 8](http://www.swiftforensics.com/2013/12/amcachehve-in-windows-8-goldmine-for.html)

