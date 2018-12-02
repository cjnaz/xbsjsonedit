# xbsjsonedit

xbsjsonedit is a limited tool for modifying backup data dumped from the 
[xBrowserSync App](https://www.xbrowsersync.org/) ([GitHub](https://github.com/xBrowserSync)).
It was originally developed for cleanup up bookmark replication problems in early releases 
of the App.  This tool may be useful for pruning duplicate bookmarks and general maintenance. 
If you want it to do other functions, grab the code and go for it.

## Usage
```
$ ./xbsjsonedit -h
usage: xbsjsonedit [-h] Infile

xBrowserSync json backup editor

positional arguments:
  Infile      json backup file

optional arguments:
  -h, --help  show this help message and exit
```

## Internal command menu
```
$ ./xbsjsonedit xBrowserSyncBackup_201811232150.json

---------------------------------------------------------------------
Options:
  s:  Search term entry (currently <__NONE__>)
  t:  List   search matches
  T:  Delete search matches

  d:  List   duplicate URLs
  D:  Delete duplicate URLs

  x:  List   tags on folders (only leaf nodes should have tags)
  X:  Delete tags on folders

  y:  List   empty folders (may arise if all children were deleted)
  Y:  Delete empty folders
  
  w:  Write out the bookmarks data to a file
  q:  Quit/exit (Do a Write first!)

Enter option: 
```
## Usage notes
Four modes are supported Listing/Deleting based on
- A search string
- Duplicate URLs in bookmarks - such as the same URL existing in two or more folders
- Folders that have tags - there is no value for folders having tags
- Empty folders - which may arise if all bookmarks were deleted from a folder

A lower case letter `t/d/x/y` will list the offenders, while an upper case letter 
`T/D/X/Y` will allow for selective deletes of the offenders. The lower case List operation
need not be run before running the upper case Delete operation.
Delete functions support individual bookmark selection, or all that match the selection mode.
You cannot do any damage to the original bookmark file during a session, so experiment and poke
around.

When deleting empty folders you may find that you need to repeat the operation a few times. 
This happens when there are folders within folders that contain no bookmarks.  The lower folder 
must be deleted before the next higher folder is seen as empty.  Just repeat the folder deletes 
until the Matches count is 0.

The output file defaults to the Infile + "_OUT", and can be specified during the write operation. 
You may want to write out intermediate editing results to save work done up to that point.
The output format is "pretty printed" json for readability, and is accepted by the 
xBrowserSync app Restore function via copy/paste.  Note: xBrowserSync will allow you to restore 
to (blast) your current
syncID, so you may want to Disable Sync, then create a new sync before restoring the modified bookmarks.



## Known issues:
- This code was developed on Python 2.7.10 on Windows, and exercised on Linux.  It is known broken 
on Python 3.
- The xBrowserSync Backup function appears to dump out Latin-1 encoding, not UTF-8.  The 
Write command from this tool outputs UTF-8, which seems to Restore without error in xBrowserSync, 
but effectively garbages up the bookmark title text for non-ASCII bookmarks.


