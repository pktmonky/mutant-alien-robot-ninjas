Troy Larson
Chris Pogue's Sniper Forensics
# Introduction
An overview of the Analysis process and common pitfalls including:
* Lack of defined goals
* Tool focused instead of process focused
* Locard's exchange principle
* Speculation
* Direct vs indirect artifacts
* Least frequency of occurance

# Preparation and Data Collection
An overview of developing checklists and questionaires. A discussion of "in scope". Carbon Black is awesome.
Data collection tools:
* DumpIt for physical memory collection
* Or FTK Imager
* Or Volatility wimpmem
* Or BelkaSoft RAM-Capturer
* FTK for special files, e.g. MFT, Registry hives, Windows Event Logs
* F-Response data acquisition of remote network connected systems

# Volume Shadow Copies (VSC)
Replaces System Restore Points and adds user data to incremental snapshots. Enabled/disabled at HKLM\SYSTEM\CurrentControlSet\Services\VSS. Configure under HKLM\SYSTEM\CurrentControlSet\Control\BackupRestore. Monitored volumes listed under HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SPP\CLients.
Tools:
* vssadmin - built-in tool to list available VSCs
* ShadowExplorer - graphical interface to browse and export files contained with a VSC
* mklink to mount, browse, and export files contained within a VSC
* ProDiscover for a commercial product
* F-Response for browsing VSC on remote systems
* VHDmount to convert and mount acquired images for browsing VSC contents

# File Analysis
## MFT
* Kyle Oetken's blog on larger MFT records http://traceevidence.blogspot.com/2013/03/a-quick-look-at-mft-resident-data-on.html
* Corey Harrell's blog post on scanning MFT for ZeroAccess http://journeyintoir.blogspot.com/2012/12/extracting-zeroaccess-from-ntfs.html
* David Kovar's python script for analyzing MFT http://www.integriography.com/ https://github.com/dkovar/analyzeMFT/
* Blog post on extracting the MFT from an acquired image http://epyxforensics.com/node/43 or TZWorks ntfswalk tool.
* Anti-forensics time-stomping http://sourceforge.net/projects/timestomp-gui/files/Windows/
* Anti-forensics with File system tunneling. 
* Chad Tilbury's blog post on the Index attribute and the name of deleted files http://forensicmethods.com/ntfs-index-attribute referencing 
* Willi BAllenthin's indxparse.py script http://www.williballenthin.com/forensics/indx/index.html

## David Cowen's TriForce Technique
Uses MFT, $LogFile, and USN Change Journal ($USNJrnl) http://hackingexposedcomputerforensicsblog.blogspot.com/2013/01/happy-new-year-new-post-ntfs-forensic.html

## Event Logs
* An overview of Harlan's tools, evtrpt.pl and evtparse.pl
* Explanation of logs http://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx
* Technique for recovering deleted event logs.
Windows Event Log moved to XML format and stored in C:\Windows\system32\winevt\Logs. 
* Microsoft's Logparser tool will view/parse the new log files and can export them to CSV format
* The format has been decoded at Andreas Schuster's blog http://computer.forensikblog.de/en/

## Recycle Bin
Disabled at HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\BitBucket.

## Prefetch Files
Disabled at HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management\PrefetchParameters\EnablePrefetcher

## Scheduled Tasks
Located in (2k, XP, 2k3) C:\Windows\Tasks or (7 & 8) C:\Windows\System32\Tasks
Logged to Microsoft-Windows-TaskScheduler/Operational Event Log

## Jump Lists
* Stored in C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations
* Personalized Jump lists C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations
* Files can be read with MiTec Structured Storage Viewer
* Can parse with jmp from https://tzworks.net/prototype_page.php?proto_id=20
* Harlan Carvey also wrote a perl script to parse Jump List files

## Hibernation Files
Volatility can analyze memory dump within hibernation files

## Application Files
* AV logs can provide insight during initial intrusion (were some files detected by AV)
* Chat logs, such as skype, might reveal insider threat communications
* Image files can contain GPS and other metadata

# Registry Analysis
Repeat of Windows Registry Forensics book
* http://kurtaubuchon.blogspot.com
* http://dfstream.blogspot.com
* http://moyix.blogspot.com
* http://dfsforensics.blogspot.com


