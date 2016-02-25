<properties
	pageTitle="Copy or move data to Storage with AzCopy | Microsoft Azure"
	description="Use the AzCopy utility to move or copy data to or from blob, table, and file content. Copy data to Azure Storage from local files, or copy data within or between storage accounts. Easily migrate your data to Azure Storage."
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="micurd"/>

# Transfer data with the AzCopy Command-Line Utility

## Overview

AzCopy is a Windows command-line utility designed for copying data to and from Microsoft Azure Blob, File, and Table storage using simple commands with optimal performance. You can copy data from one object to another within your storage account, or between storage accounts.

> [AZURE.NOTE] This guide assumes that you are already familiar with [Azure Storage](https://azure.microsoft.com/services/storage/). If not, reading the [Introduction to Azure Storage](storage-introduction.md) documentation will be helpful. Most importantly, you will need to [create a Storage account](storage-create-storage-account.md#create-a-storage-account) in order to start using AzCopy.

## Download and install AzCopy

### Windows

Download the [latest version of AzCopy](http://aka.ms/downloadazcopy).

### Mac/Linux

AzCopy is not available for Mac/Linux OSs. However, Azure CLI is a suitable alternative for copying data to and from Azure Storage. Read [Using the Azure CLI with Azure Storage](storage-azure-cli.md) to learn more.

## Writing your first AzCopy command

The basic syntax for AzCopy commands is:

	AzCopy /Source:<source> /Dest:<destination> [Options]

Open a command window and navigate to the AzCopy installation directory on your computer - where the `AzCopy.exe` executable is located. If desired, you can add the AzCopy installation location to your system path. By default, AzCopy is installed to `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` (64-bit Windows) or `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy` (32-bit Windows).

The following examples demonstrate a variety of scenarios for copying data to and from Microsoft Azure Blobs, Files, and Tables. Refer to the [AzCopy Parameters](#azcopy-parameters) section for a detailed explanation of the parameters used in each sample.

## Blob: Upload

### Upload single file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

If the specified destination container does not exist, AzCopy will create it and upload the file into it.

### Upload single file to virtual directory

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

If the specified virtual directory does not exist, AzCopy will upload the file to include the virtual directory in its name (*e.g.*, `vd/abc.txt` in the example above).

### Upload all files

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Specifying option `/S` copies the contents of the specified directory to Blob storage recursively, meaning that all subfolders and their files will be copied as well. For instance, assume the following files reside in folder `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

After the copy operation, the container will include the following files:

  	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

If you do not specify option `/S`, AzCopy will not copy recursively. After the copy operation, the container will include the following files:

	abc.txt
	abc1.txt
	abc2.txt

### Upload files matching specified pattern

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Assume the following files reside in folder `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

After the copy operation, the container will include the following files:

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

If you do not specify option `/S`, AzCopy will will only copy blobs that don't reside in a virtual directory:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

### Specify the MIME content type of a destination blob

By default, AzCopy sets the content type of a destination blob to `application/octet-stream`. Beginning with version 3.1.0, you can explicitly specify the content type via the option `/SetContentType:[content-type]`. This syntax sets the content type for all blobs in a copy operation.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

If you specify `/SetContentType` without a value, then AzCopy will set each blob or file's content type according to its file extension.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## Blob: Download

### Download single blob

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Note that if the folder `C:\myfolder` does not exist, AzCopy will create it and download `abc.txt ` into the new folder.

### Download single blob from secondary region

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

You must have read-access geo-redundant storage enabled.

### Download all blobs

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Assume the following blobs reside in the specified container:  

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

After the copy operation, the directory `C:\myfolder` will include the following files:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

If you do not specify option `/S`, AzCopy will will only copy blobs that don't reside in a virtual directory:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

### Download blobs with specified prefix

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Assume the following blobs reside in the specified container. All blobs beginning with the prefix `a` will be copied:

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

After the copy operation, the folder `C:\myfolder` will include the following files:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

The prefix applies to the virtual directory, which forms the first part of the blob name. In the example shown above, the virtual directory does not match the specified prefix, so it is not copied. In addition, if the option `\S` is not specified, AzCopy will not copy any blobs.

### Set the last-modified time of downloaded files to be same as the source blobs

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

You can also exclude blobs from the copy operation based on their last-modified time. For example, if you want to exclude blobs whose last modified time is the same or newer than the destination file, add the `/XN` option:

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Or if you want to exclude blobs whose last modified time is the same or older than the destination file, add the `/XO` option:

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## Blob: Copy

### Copy single blob within Storage account

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

When you copy a blob within a Storage account, a [server-side copy]((http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)) operation is performed.

### Copy single blob across Storage accounts

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

When you copy a blob across Storage accounts, a [server-side copy]((http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)) operation is performed.

### Copy single blob from secondary region to primary region

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

You must have read-access geo-redundant storage enabled.

### Copy single blob and its snapshots across Storage accounts

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

After the copy operation, the target container will include the blob and its snapshots. Assuming the blob in the example above has two snapshots, the container will include the following blob and snapshots:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt

### Synchronously copy blobs across Storage accounts

AzCopy by default copies data between two storage endpoints asynchronously. Therefore, the copy operation will run in the background using spare bandwidth capacity that has no SLA in terms of how fast a blob will be copied, and AzCopy will periodically check the copy status until the copying is completed or failed.

The `/SyncCopy` option ensures that the copy operation will get consistent speed. AzCopy performs the synchronous copy by downloading the blobs to copy from the specified source to local memory, and then uploading them to the Blob storage destination.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy` might generate additional egress cost compared to asynchronous copy, the recommended approach is to use this option in an Azure VM that is in the same region as your source storage account to avoid egress cost.

## File: Upload

### Upload single file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### Upload all files

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Empty folders will not be copied.

### Upload files matching specified pattern

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## File: Download

### Download single file

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

If the specified source is an Azure file share, then you must either specify the exact file name, (*e.g.* `abc.txt`) to copy a single file, or specify option `/S` to copy all files in the share recursively. Attempting to specify both a file pattern and option `/S` together will result in an error.

### Download all files

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Note that any empty folders will not be copied.

## File: Copy

### Copy across file shares

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### Copy from file share to blob

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Note that asynchronous copying from File Storage to Page Blob is not supported.

### Copy from blob to file share

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### Synchronously copy files

You can specify the `/SyncCopy` option to copy data from File Storage to File Storage, from File Storage to Blob Storage and from Blob Storage to File Storage synchronously, AzCopy does this by downloading the source data to local memory and upload it again to destination.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

When copying from File Storage to Blob Storage, the default blob type is block blob, user can specify option `/BlobType:page` to change the destination blob type.

Note that `/SyncCopy` might generate additional egress cost comparing to asynchronous copy, the recommended approach is to use this option in the Azure VM which is in the same region as your source storage account to avoid egress cost.

## Table: Upload

### Upload table

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

The option `/EntityOperation` indicates how to insert entities into the table. Possible values are:

- `InsertOrSkip`: Skips an existing entity or inserts a new entity if it does not exist in the table.
- `InsertOrMerge`: Merges an existing entity or inserts a new entity if it does not exist in the table.
- `InsertOrReplace`: Replaces an existing entity or inserts a new entity if it does not exist in the table.

Note that you cannot specify option `/PKRS` in the upload scenario. Unlike the download scenario, in which you must specify option `/PKRS` to start concurrent operations, AzCopy will by default start concurrent operations when you upload a table. The default number of concurrent operations started is equal to the number of core processors; however, you can specify a different number of concurrent with option `/NC`. For more details, type `AzCopy /?:NC` at the command line.

Note that asynchronous copying from File Storage to Page Blob is not supported.

## Table: Download

### Download table

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy writes a manifest file to the specified destination folder. The manifest file is used in the upload process to locate the necessary data files and perform data validation. The manifest file uses the following naming convention by default:

	<account name>_<table name>_<timestamp>.manifest

User can also specify the option `/Manifest:<manifest file name>` to set the manifest file name.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### Download table to JSON or CSV data file format

AzCopy by default downloads tables to the JSON data files. You can specify the option `/PayloadFormat:JSON|CSV` to decide the downloaded file type.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

When specifying the CSV payload format, besides the data files with `.csv` extension that will be found in the place specified by the parameter `/Dest`, AzCopy will generate scheme file with file extension `.schema.csv` for each data file.

### Download table entities concurrently

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy will start concurrent operations to export entities when the user specifies option `/PKRS`. Each operation exports one partition key range.

Note that the number of concurrent operations is also controlled by option `/NC`. AzCopy uses the number of core processors as the default value of `/NC` when copying table entities, even if `/NC` was not specified. When the user specifies option `/PKRS`, AzCopy uses the smaller of the two values - partition key ranges versus implicitly or explicitly specified concurrent operations - to determine the number of concurrent operations to start. For more details, type `AzCopy /?:NC` at the command line.

### Split download into multiple files

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy uses a *volume index* in the split data file names to distinguish multiple files. The volume index consists of two parts, a *partition key range index* and a *split file index*. Both indexes are zero-based.

The partition key range index will be 0 if user does not specify option `/PKRS`.

For instance, suppose AzCopy generates two data files after the user specifies option `/SplitSize`. The resulting data file names might be:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Note that the minimum possible value for option `/SplitSize` is 32MB. If the specified destination is Blob storage, AzCopy will split the data file once its sizes reaches the blob size limitation (200GB), regardless of whether option `/SplitSize` has been specified by the user.

## Table: Copy

### Copy table to blob

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy will generate a JSON data file into the local folder or blob container with following naming convention:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

The generated JSON data file follows the payload format for minimal metadata. For details on this payload format, see [Payload Format for Table Service Operations](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Note that when copying Table entities to a blob, AzCopy will download the Table entities to local temporary data files and then upload those entities to the blob. These temporary data files are put into the journal file folder with the default path “<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>”, you can specify option /Z:[journal-file-folder] to change the journal file folder location and thus change the temporary data files location. The temporary data files’ size is decided by your table entities’ size and the size you specified with the option /SplitSize, although the temporary data file in local disk will be deleted instantly once it has been uploaded to the blob, please make sure you have enough local disk space to store these temporary data files before they are deleted.

### Copy entities to table using blobs

Imagine a Blob container with the following blobs: A JSON file representing an Azure Table and its accompanying manifest file.

	myaccount_mytable_20140103T112020.manifest
	myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

You can run the following command to copy entities to a table using the manifest file in that blob container:

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## Other AzCopy features

### Use a response file to specify command-line parameters

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

You can include any AzCopy command-line parameters in a response file. AzCopy processes the parameters in the file as if they had been specified on the command line, performing a direct substitution with the contents of the file.

Assume a response file named `copyoperation.txt`, that contains the following lines. Each AzCopy parameter can be specified on a single line

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

or on separate lines:

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S
	/Y

AzCopy will fail if you split the parameter across two lines, as shown here for the `/sourcekey` parameter:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	<sourcekey>
	/S
	/Y

### Use multiple response files to specify command-line parameters

Assume a response file named `source.txt` that specifies a source container:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

And a response file named `dest.txt` that specifies a destination folder in the file system:

	/Dest:C:\myfolder

And a response file named `options.txt` that specifies options for AzCopy:

	/S /Y

To call AzCopy with these response files, all of which reside in a directory `C:\responsefiles`, use this command:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy processes this command just as it would if you included all of the individual parameters on the command line:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### Specify a shared access signature (SAS)

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

You can also specify a SAS on the container URI:

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### Journal file folder

Each time you issue a command to AzCopy, it checks whether a journal file exists in the default folder, or whether it exists in a folder that you specified via this option. If the journal file does not exist in either place, AzCopy treats the operation as new and generates a new journal file.

If the journal file does exist, AzCopy will check whether the command line that you input matches the command line in the journal file. If the two command lines match, AzCopy resumes the incomplete operation. If they do not match, you will be prompted to either overwrite the journal file to start a new operation, or to cancel the current operation.

If you want to use the default location for the journal file:

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

If you omit option `/Z`, or specify option `/Z` without the folder path, as shown above, AzCopy creates the journal file in the default location, which is `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. If the journal file already exists, then AzCopy resumes the operation based on the journal file.

If you want to specify a custom location for the journal file:

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

This example creates the journal file if it does not already exist. If it does exist, then AzCopy resumes the operation based on the journal file.

If you want to resume an AzCopy operation:

	AzCopy /Z:C:\journalfolder\

This example resumes the last operation, which may have failed to complete.

### Generate a log file

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

If you specify option `/V` without providing a file path to the verbose log, then AzCopy creates the log file in the default location, which is `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Otherwise, you can create an log file in a custom location:

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Note that if you specify a relative path following option `/V`, such as `/V:test/azcopy1.log`, then the verbose log is created in the current working directory within a subfolder named `test`.

### Specify the number of concurrent operations to start

Option `/NC` specifies the number of concurrent copy operations. By default, AzCopy will begin concurrent operations at eight times the number of core processors you have. If you are running AzCopy across a low-bandwidth network, you can specify a lower number for this option to avoid failure caused by resource competition.

### Run AzCopy against Azure Storage Emulator

You can run AzCopy against the [Azure Storage Emulator](storage-use-emulator.md) for Blobs:

	AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

and Tables:

	AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## AzCopy Parameters

Parameters for AzCopy are described below. You can also type one of the following commands from the command line for help in using AzCopy:

- For detailed command-line help for AzCopy: `AzCopy /?`
- For detailed help with any AzCopy parameter: `AzCopy /?:SourceKey`
- For command-line examples: `AzCopy /?:Samples`

### /Source:

Specifies the source data from which to copy. The source can be a file system directory, a blob container, a blob virtual directory, a storage file share, a storage file directory, or an Azure table.

**Applicable to:** Blobs, Files, Tables

### /Dest:

Specifies the destination to copy to. The destination can be a file system directory, a blob container, a blob virtual directory, a storage file share, a storage file directory, or an Azure table.

**Applicable to:** Blobs, Files, Tables

### /Pattern:

Specifies a file pattern that indicates which files to copy. The behavior of the /Pattern parameter is determined by the location of the source data, and the presence of the recursive mode option. Recursive mode is specified via option /S.

If the specified source is a directory in the file system, then standard wildcards are in effect, and the file pattern provided is matched against files within the directory. If option /S is specified, then AzCopy also matches the specified pattern against all files in any subfolders beneath the directory.

If the specified source is a blob container or virtual directory, then wildcards are not applied. If option /S is specified, then AzCopy interprets the specified file pattern as a blob prefix. If option /S is not specified, then AzCopy matches the file pattern against exact blob names.

If the specified source is an Azure file share, then you must either specify the exact file name, (e.g. abc.txt) to copy a single file, or specify option /S to copy all files in the share recursively. Attempting to specify both a file pattern and option /S together will result in an error.

AzCopy uses case-sensitive matching when the /Source is a blob container or blob virtual directory, and uses case-insensitive matching in all the other cases.

The default file pattern used when no file pattern is specified is *.* for a file system location or an empty prefix for an Azure Storage location. Specifying multiple file patterns is not supported.

**Applicable to:** Blobs, Files

## Known Issues and Best Practices

### Limit concurrent writes while copying data

When you copy blobs or files with AzCopy, keep in mind that another application may be modifying the data while you are copying it. If possible, ensure that the data you are copying is not being modified during the copy operation. For example, when copying a VHD associated with an Azure virtual machine, make sure that no other applications are currently writing to the VHD. A good way to do this is by leasing the resource to be copied. Alternately, you can create a snapshot of the VHD first and then copy the snapshot.

If you cannot prevent other applications from writing to blobs or files while they are being copied, then keep in mind that by the time the job finishes, the copied resources may no longer have full parity with the source resources.

### Run one AzCopy instance on one machine.
AzCopy is designed to maximize the utilization of your machine resource to accelerate the data transfer, we recommend you run only one AzCopy instance on one machine, and specify the option `/NC` if you need more concurrent operations. For more details, type `AzCopy /?:NC` at the command line.

### Enable FIPS compliant MD5 algorithms for AzCopy when you "Use FIPS compliant algorithms for encryption, hashing and signing".
AzCopy by default uses .NET MD5 implementation to calculate the MD5 when copying objects, but there are some security requirements that need AzCopy to enable FIPS compliant MD5 setting.

You can create an app.config file `AzCopy.exe.config` with property `AzureStorageUseV1MD5` and put it aside with AzCopy.exe.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <appSettings>
	    <add key="AzureStorageUseV1MD5" value="false"/>
	  </appSettings>
	</configuration>

For property “AzureStorageUseV1MD5”
• True - The default value, AzCopy will use .NET MD5 implementation.
• False – AzCopy will use FIPS compliant MD5 algorithm.

Note that FIPS compliant algorithms is disabled by default on your Windows machine, you can type secpol.msc in your Run window and check this switch at Security Setting->Local Policy->Security Options->System cryptography: Use FIPS compliant algorithms for encryption, hashing and signing.

## AzCopy versions

> [AZURE.NOTE] We recommand you install the latest version of AzCopy to get new features and better performance.

| Version | What's New                                                                                      				| Referenced .NET Client Library Version | Target Storage REST API Version |
|---------|-----------------------------------------------------------------------------------------------------------------|--------|----------|
| [**V5.0.0**](http://xdmrelease.blob.core.windows.net/azcopy-5-0-0/MicrosoftAzureStorageTools.msi)  | **Current release version. Includes all functionality from V4.2.0. All features for File and Table storage are now GA.** | **V6.0.0** | **2015-04-05**
| [V4.2.0](http://xdmrelease.blob.core.windows.net/azcopy-4-2-0-preview/MicrosoftAzureStorageTools.msi)  | Includes all the functionality from V3.2.0. Also supports File Storage Share SAS, File Storage asynchronous copying, exporting Table entities to CSV and specifying manifest name when exporting Table entities | V5.0.0 | 2015-02-21
| [V3.2.0](http://xdmrelease.blob.core.windows.net/azcopy-3-2-0/MicrosoftAzureStorageTools.msi)  | Supports Append Blob and FIPS compliant MD5 Setting | V5.0.0 | 2015-02-21
| [V4.1.0](http://xdmrelease.blob.core.windows.net/azcopy-4-1-0-preview/MicrosoftAzureStorageTools.msi)  | Includes all the functionality from V3.1.0. Supports synchronously copying blobs and files and specifying content type for destination blobs and files | V4.3.0 | 2014-02-14
| [V3.1.0](http://xdmrelease.blob.core.windows.net/azcopy-3-1-0/MicrosoftAzureStorageTools.msi)  | Supports synchronously copying blobs and specifying content type for destination blobs.| V4.3.0 | 2014-02-14
| [V4.0.0](http://xdmrelease.blob.core.windows.net/azcopy-4-0-0-preview/MicrosoftAzureStorageTools.msi)  | Includes all the functionality from V3.0.0. Also supports copying files to or from Azure File storage, and copying entities to or from Azure Table storage.| V4.2.1 | 2014-02-14
| [V3.0.0](http://xdmrelease.blob.core.windows.net/azcopy-3-0-0/MicrosoftAzureStorageTools.msi)  | Modifies AzCopy command-line syntax to require parameter names, and redesigns the command-line help. This version only supports copying to and from Azure Blob storage.| V4.2.1 | 2014-02-14
| V2.5.1  | Optimizes performance when using options /xo and /xn. Fixes bugs related to special characters in source file names and journal file corruption after user input the wrong command-line syntax.| V4.1.0 | 2014-02-14
| V2.5.0  | Optimizes performance for large-scale copy scenarios, and introduces several important usability improvements.| V4.1.0 | 2014-02-14
| V2.4.1  | Supports specifying the destination folder in the installation wizard.| V4.0.0 | 2014-02-14
| V2.4.0  | Supports uploading and downloading files for Azure File storage.| V4.0.0 | 2014-02-14
| V2.3.0  | Supports read-access geo-redundant storage accounts.| V3.0.3 | 2013-08-15
| V2.2.2  | Upgraded to use Azure storage client library version 3.0.3.| V3.0.3 | 2013-08-15
| V2.2.1  | Fixed performance issue when copying large amount files within same storage account.| V2.1.0 |
| V2.2    | Supports setting the virtual directory delimiter for blob names. Supports specifying the journal file path.| V2.1.0 |
| V2.1    | Provides more than 20 options to support blob upload, download, and copy operations in an efficient way.| V2.0.5 |


## Next steps

For more information about Azure Storage and AzCopy, see the following resources.

### Azure Storage documentation:

- [Introduction to Azure Storage](storage-introduction.md)
- [How to use Blob storage from .NET](storage-dotnet-how-to-use-blobs.md)
- [How to use File storage from .NET](storage-dotnet-how-to-use-files.md)
- [How to use Table storage from .NET](storage-dotnet-how-to-use-tables.md)
- [How to create, manage, or delete a storage account](storage-create-storage-account.md)

### Azure Storage blog posts:
- [Introducing Azure Storage Data Movement Library Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Introducing synchronous copy and customized content type](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Optimized for Large-Scale Copy Scenarios](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: Support for read-access geo-redundant storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Transfer data with re-startable mode and SAS token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Using cross-account Copy Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Uploading/downloading files for Azure Blobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
