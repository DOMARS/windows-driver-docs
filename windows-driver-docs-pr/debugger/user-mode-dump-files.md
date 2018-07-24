---
title: User-Mode Dump Files
description: User-Mode Dump Files
ms.assetid: bef29d75-6620-4219-b402-36fbddc4fe1f
keywords: ["dump file", "user-mode", "user-mode file types", "full user-mode dump", "creating user-mode dump file", "WinDbg", "CDB", "UserDump", "User Mode Process Dump tool"]
ms.author: domars
ms.date: 05/23/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
---

# User-Mode Dump Files

## Varieties of User-Mode Dump Files

There are several kinds of user-mode crash dump files, but they are divided into two categories:

- Full User-Mode Dumps
- Minidumps

The difference between these dump files is one of size. Minidumps are usually more compact, and can be easily sent to an analyst.

**Note**   Much information can be obtained by analyzing a dump file. However, no dump file can provide as much information as actually debugging the crash directly with a debugger.

### Full User-Mode Dumps

A *full user-mode dump* is the basic user-mode dump file.

This dump file includes the entire memory space of a process, the program's executable image itself, the handle table, and other information that will be useful to the debugger.

It is possible to "shrink" a full user-mode dump file into a minidump. Simply load the dump file into the debugger and then use the [**.dump (Create Dump File)**](-dump--create-dump-file-.md) command to save a new dump file in minidump format.

**Note**   Despite their names, the largest "minidump" file actually contains more information than the full user-mode dump. For example, **.dump /mf** or **.dump /ma** will create a larger and more complete file than **.dump /f**.

In user mode, **.dump /m\[***MiniOptions***\]** is the best choice. The dump files created with this switch can vary in size from very small to very large. By specifying the proper *MiniOptions* you can control exactly what information is included.

### Minidumps

A user-mode dump file that includes only selected parts of the memory associated with a process is called a *minidump*.

The size and contents of a minidump file vary depending on the program being dumped and the application doing the dumping. Sometimes, a minidump file is fairly large and includes the full memory and handle table. Other times, it is much smaller -- for example, it might only contain information about a single thread, or only contain information about modules that are actually referenced in the stack.

The name "minidump" is misleading, because the largest minidump files actually contain more information than the "full" user-mode dump. For example, **.dump /mf** or **.dump /ma** will create a larger and more complete file than **.dump /f**. For this reason, **.dump /m**\[*MiniOptions*\] recommended over **.dump /f** for all user-mode dump file creation.

If you are creating a minidump file with the debugger, you can choose exactly what information to include. A simple **.dump /m** command will include basic information about the loaded modules that make up the target process, thread information, and stack information. This can be modified by using any of the following options:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">.dump option</th>
<th align="left">Effect on dump file</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><strong>/ma</strong></p></td>
<td align="left"><p>Creates a minidump with all optional additions. The <strong>/ma</strong> option is equivalent to <strong>/mfFhut</strong> -- it adds full memory data, handle data, unloaded module information, basic memory information, and thread time information to the minidump.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/mf</strong></p></td>
<td align="left"><p>Adds full memory data to the minidump. All accessible committed pages owned by the target application will be included.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>/mF</strong></p></td>
<td align="left"><p>Adds all basic memory information to the minidump. This adds a stream to the minidump that contains all basic memory information, not just information about valid memory. This allows the debugger to reconstruct the complete virtual memory layout of the process when the minidump is being debugged.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/mh</strong></p></td>
<td align="left"><p>Adds data about the handles associated with the target application to the minidump.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>/mu</strong></p></td>
<td align="left"><p>Adds unloaded module information to the minidump. This is only available in Windows Server 2003 and later versions of Windows.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/mt</strong></p></td>
<td align="left"><p>Adds additional thread information to the minidump. This includes thread times, which can be displayed by using <strong>[.ttime (Display Thread Times)](-ttime--display-thread-times-.md)</strong> when debugging the minidump.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>/mi</strong></p></td>
<td align="left"><p>Adds <em>secondary memory</em> to the minidump. Secondary memory is any memory referenced by a pointer on the stack or backing store, plus a small region surrounding this address.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/mp</strong></p></td>
<td align="left"><p>Adds process environment block (PEB) and thread environment block (TEB) data to the minidump. This can be useful if you need access to Windows system information regarding the application's processes and threads.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>/mw</strong></p></td>
<td align="left"><p>Adds all committed read-write private pages to the minidump.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/md</strong></p></td>
<td align="left"><p>Adds all read-write data segments within the executable image to the minidump.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>/mc</strong></p></td>
<td align="left"><p>Adds code sections within images.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/mr</strong></p></td>
<td align="left"><p>Deletes from the minidump those portions of the stack and store memory that are not useful for recreating the stack trace. Local variables and other data type values are deleted as well. This option does not make the minidump smaller (since these memory sections are simply zeroed), but it is useful if you wish to protect the privacy of other applications.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>/mR</strong></p></td>
<td align="left"><p>Deletes the full module paths from the minidump. Only the module <em>names</em> will be included. This is a useful option if you wish to protect the privacy of the user's directory structure.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>/mk &quot;</strong> <em>FileName</em> <strong>&quot;</strong></p></td>
<td align="left"><p>(Windows Vista only) Creates a kernel-mode minidump in addition to the user-mode minidump. The kernel-mode minidump will be restricted to the same threads that are stored in the user-mode minidump. <em>FileName</em> must be enclosed in quotation marks.</p></td>
</tr>
</tbody>
</table>

These options can be combined. For example, the command **.dump /mfiu** can be used to create a fairly large minidump, or the command **.dump /mrR** can be used to create a minidump that preserves the user's privacy. For full syntax details, see [**.dump (Create Dump File)**](-dump--create-dump-file-.md).

For details on the internals of minidump files, see the DbgHelp Reference in the Microsoft Windows SDK.

## Creating a User-Mode Dump File

There are several different tools that can be used to create a user-mode dump file: CDB, WinDbg, Windows Error Reporting (WER), UserDump, and ADPlus.

For information about creating a user-mode dump file through ADPlus, see [ADPlus](adplus.md).

For information about creating a user-mode dump file through WER, see [Windows Error Reporting](windows-error-reporting.md).

### Choosing the Best Tool

There are severa; different tools that can create user-mode dump files. In most cases, ADPlus is the best tool to use.

The following table shows the features of each tool.

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Feature</th>
<th align="left">[ADPlus](adplus.md)</th>
<th align="left">[Windows Error Reporting](windows-error-reporting.md)</th>
<th align="left">[CDB and WinDbg](cdb-and-windbg.md)</th>
<th align="left">[UserDump](userdump.md)</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Creating a dump file when an application crashes (postmortem debugging)</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
</tr>
<tr class="even">
<td align="left"><p>Creating a dump file when an application &quot;hangs&quot; (stops responding but does not actually crash)</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Creating a dump file when an application encounters an exception</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
</tr>
<tr class="even">
<td align="left"><p>Creating a dump file while an application is running normally</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>No</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Creating a dump file from an application that fails during startup</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>Yes</p></td>
</tr>
<tr class="even">
<td align="left"><p>Shrinking an existing dump file</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>No</p></td>
<td align="left"><p>Yes</p></td>
<td align="left"><p>No</p></td>
</tr>
</tbody>
</table>

### CDB and WinDbg

CDB and WinDbg can create user-mode dump files in a variety of ways.

- **Creating a Dump File Automatically:** When an application error occurs, Windows can respond in several different ways, depending on the postmortem debugging settings. If these settings instruct a debugging tool to create a dump file, a user-mode memory dump file will be created. For more information, see [Enabling Postmortem Debugging](enabling-postmortem-debugging.md).

- **Creating Dump Files While Debugging:** When CDB or WinDbg is debugging a user-mode application, you can also the [**.dump (Create Dump File)**](-dump--create-dump-file-.md) command to create a dump file.

   This command does not cause the target application to terminate. By selecting the proper command options, you can create a minidump file that contains exactly the amount of information you wish.

- **Shrinking an Existing Dump File:** CDB and WinDbg can also be used to *shrink* a dump file. To do this, begin debugging an existing dump file, and then use the **.dump** command to create a dump file of smaller size.


### UserDump

The UserDump tool (Userdump.exe), also known as User-Mode Process Dump, can create user-mode dump files.

UserDump and its documentation are part of the OEM Support Tools package.

For more info and to download these tools, see [How to use the Userdump.exe tool to create a dump file](http://go.microsoft.com/fwlink/p/?LinkId=241339) and follow the instructions on that page. Additionally, When CDB or WinDbg is debugging a user-mode application, you can also use the [.dump (Create Dump File) command](-dump--create-dump-file-.md) to create a dump file.

## Analyzing a User-Mode Dump File

### Installing Symbol Files

Before analyzing the memory dump file, you will need to install the symbol files for the version of Windows that generated the dump file. These files will be used by the debugger you choose to use to analyze the dump file. For more information about the proper installation of symbol files, see [Installing Windows Symbol Files](installing-windows-symbol-files.md).

You will also need to install all the symbol files for the user-mode process, either an application or system service, that caused the system to generate the dump file. If this code was written by you, the symbol files should have been generated when the code was compiled and linked. If this is commercial code, check on the product CD-ROM or contact the software manufacturer for these particular symbol files.

### Analyzing a User-Mode Dump File with CDB

User-mode memory dump files can be analyzed by CDB. The processor or Windows version that the dump file was created on does not need to match the platform on which CDB is being run.

#### Starting CDB

To analyze a dump file, start CDB with the **-z** command-line option:

**cdb -y** *SymbolPath* **-i** *ImagePath* **-z** *DumpFileName*

The **-v** option (verbose mode) is also useful. For a full list of options, see [**CDB Command-Line Options**](cdb-command-line-options.md).

You can also open a dump file after the debugger is running by using the [**.opendump (Open Dump File)**](-opendump--open-dump-file-.md) command, followed with [**g (Go)**](g--go-.md). This allows you to debug multiple dump files at the same time.

It is possible to debug multiple dump files at the same time. This can be done by including multiple **-z** switches on the command line (each followed by a different file name), or by using [**.opendump**](-opendump--open-dump-file-.md) to add additional dump files as debugger targets. For information about how to control a multiple-target session, see [Debugging Multiple Targets](debugging-multiple-targets.md).

Dump files generally end with the extension .dmp or .mdmp. You can use network shares or Universal Naming Convention (UNC) file names for the memory dump file.

It is also common for dump files to be packed into a CAB file. If you specify the file name (including the .cab extension) after the **-z** option or as the argument to an [**.opendump**](-opendump--open-dump-file-.md) command, the debugger can read the dump files directly out of the CAB. However, if there are multiple dump files stored in a single CAB, the debugger will only be able to read one of them. The debugger will not read any additional files from the CAB, even if they are symbol files or executables associated with the dump file.

#### Analyzing a Full User Dump File

Analysis of a full user dump file is similar to analysis of a live debugging session. See the [Debugger Commands](debugger-commands.md) reference section for details on which commands are available for debugging dump files in user mode.

#### Analyzing Minidump Files

Analysis of a user-mode minidump file is done in the same way as a full user dump. However, since much less memory has been preserved, you are much more limited in the actions you can perform. Commands that attempt to access memory beyond what is preserved in the minidump file will not function properly.

### Analyzing a User-Mode Dump File with WinDbg

User-mode memory dump files can be analyzed by WinDbg. The processor or Windows version that the dump file was created on does not need to match the platform on which WinDbg is being run.

To analyze a dump file, start WinDbg with the **-z** command-line option:

**windbg -y** *SymbolPath* **-i** *ImagePath* **-z** *DumpFileName*

The **-v** option (verbose mode) is also useful. For a full list of options, see [**WinDbg Command-Line Options**](windbg-command-line-options.md).

If WinDbg is already running and is in dormant mode, you can open a crash dump by selecting the **File | Open Crash Dump** menu command or pressing the CTRL+D shortcut key. When the **Open Crash Dump** dialog box appears, enter the full path and name of the crash dump file in the **File name** text box, or use the dialog box to select the proper path and file name. When the proper file has been chosen, click **Open**.

You can also open a dump file after the debugger is running by using the [**.opendump (Open Dump File)**](-opendump--open-dump-file-.md) command, followed with [**g (Go)**](g--go-.md).

It is possible to debug multiple dump files at the same time. This can be done by including multiple **-z** switches on the command line (each followed by a different file name), or by using [**.opendump**](-opendump--open-dump-file-.md) to add additional dump files as debugger targets. For information about how to control a multiple-target session, see [Debugging Multiple Targets](debugging-multiple-targets.md).

Dump files generally end with the extension .dmp or .mdmp. You can use network shares or Universal Naming Convention (UNC) file names for the memory dump file.

It is also common for dump files to be packed into a CAB file. If you specify the file name (including the .cab extension) after the **-z** option or as the argument to an [**.opendump**](-opendump--open-dump-file-.md) command, the debugger can read the dump files directly out of the CAB. However, if there are multiple dump files stored in a single CAB, the debugger will only be able to read one of them. The debugger will not read any additional files from the CAB, even if they were symbol files or executables associated with the dump file.

#### Analyzing a Full User Dump File

Analysis of a full user dump file is similar to analysis of a live debugging session. See the [Debugger Commands](debugger-commands.md) reference section for details on which commands are available for debugging dump files in user mode.

#### Analyzing Minidump Files

Analysis of a user-mode minidump file is done in the same way as a full user dump. However, since much less memory has been preserved, you are much more limited in the actions you can perform. Commands that attempt to access memory beyond what is preserved in the minidump file will not function properly.

### Additional Techniques

For techniques that can be used to read specific kinds of information from a dump file, see [Extracting Information from a Dump File](extracting-information-from-a-dump-file.md).
