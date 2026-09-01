# diskutil
A program to copy files from and to Commodore disk images

USP: Can make files on physical disks on MEGA65 a lot faster to read.

Note: This program is written in Ruby. You'll need to have Ruby 3 installed on your computer to use it.

# Features

Diskutil is a commandline tool to manipulate d81 and d64 disk images. It supports:
* Creating new disk images, as well as manipulating existing disk images
* Copying files from and to disk images, using a highly optimized interleave scheme, making floppy reads ~5 times faster than normal
* While copying a file, it can add or remove two bytes at the start for the loading address
* Listing the contents of a disk image
* Changing the name and ID of a disk image
* Deleting files from a disk image

When writing files to a d81 disk image, the program will use a custom interleave scheme (described at (https://files.mega65.org?ar=c134d07e-01fc-4d03-b584-e2369722d203) ). If the disk image is copied to a physical diskette (using the BACKUP command, not file copying!), and the diskette is used with a MEGA65, this interleave scheme makes file reads about 5x faster than with the default interleave scheme. If you want to use the default interleave scheme anyway, specify -defint on the command line.

# Limitations

* Can only copy PRG files and SEQ files. Other file types can exist on the disk images it operates on.
* For safety reasons, it won't read or write files that start with . in the file system, and it won't copy files that contain / or \ from a disk image to the file system.

Some sample commands:

```
diskutil.rb newdisk.d81 -writeseq -copyf1 sound/* -list (Create disk image newdisk.d81, copy all files from the sound folder to the disk image as SEQ files, list the contents of the disk.

diskutil.rb olddisk.d81 newdisk.d81 -copy12 '*' (Load disk image olddisk.d81, create disk image newdisk.d81, copy all files from olddisk.d81 to newdisk.d81
```

This is the documentation which the program will print if you run it without arguments:

```
Usage: diskutil.rb <disk_image_1> [disk_image_2]
    [-list[1|2]]                              # List directory
    [-name[1|2] <name>]                       # Set disk name
    [-id[1|2] <id>]                           # Set disk ID
    [-out <folder_name>]                      # Specify an output folder
    [-minusok]                                # Allow patterns/names starting with '-'
    [-defint]                                 # Use default interleave for d81
    [-write<prg|seq|default>]                 # Set output file type
    [-copy<f|1|2><f|1|2>[+hhhh|-] <pattern>+] # Copy file(s)
    [-del[1|2] <pattern>+]                    # Delete file(s)
  disk_image_1: New or existing file, e.g. disk.d81 or disk.d64
  disk_image_2: New or existing file, e.g. disk2.d81 (optional)

NOTE:
  * Patterns for matching files in disk images must be given in single
    quotes, if they contain * or ?
  * A -write command is active until the next -write command
  * The output folder (if any) is created first, regardless where in
    the command line it is specified. No more than one output folder
    can be given.
  * Unless you use the -defint command, an interleave scheme optimized
     for MEGA65 is used when writing files to d81 disk images.

Sample commands:
    -list                   List all files on disk image 1
    -name "my disk"         Set the disk name of disk image 1
    -id2 AB                 Set the disk ID of disk image 2
    -copyf1 *.txt readme    Copy from file system to disk image 1
    -copy1f- 'intro*'       Copy from disk image 1 to file system, removing the load address
    -copyf2+0801 files\*    Copy from file system to disk image 2, adding a load address
    -del2 <pattern>         Delete from disk image 2
```
