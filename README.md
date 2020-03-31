# xbsjsonedit

xbsjsonedit is a limited tool for modifying backup data dumped from the 
[xBrowserSync App](https://www.xbrowsersync.org/) ([GitHub](https://github.com/xBrowserSync)).
It was originally developed for cleanup up bookmark replication problems in early releases 
of the App.  This tool may be useful for pruning duplicate bookmarks and general maintenance. 
If you want it to do other functions, grab the code and go for it.

## Usage
```
$ ./xbsjsonedit -h
usage: xbsjsonedit [-h] [--print] [--tags TAGS] [--names] [-V] Infile

xBrowserSync json backup editor

positional arguments:
  Infile                json backup file.

optional arguments:
  -h, --help            show this help message and exit
  --print, -p           Print bookmark hierarchy (redirect to less or a file).
  --tags TAGS, -t TAGS  Print tags list filtered by tags with a minimum <n> number of uses.  n=0 prints only single use tags.
  --names, -n           Used with --tags to enable printing the titles/names for each tag.
  -V, --version         Return version number and exit.
```

## Internal command menu
```
$ ./xbsjsonedit xBrowserSyncBackup_201811232150.json

---------------------------------------------------------------------
Options:
  p:  Print the bookmarks tree hierarchy

  s:  Search term entry (currently <weather>)
  t:  List   bookmark search matches
  T:  Delete bookmark search matches
  g:  List   folder names search matches
  G:  Delete folder names search matches

  d:  List   duplicate URLs
  D:  Delete duplicate URLs

  f:  List   duplicate folders
  F:  Delete duplicate folders

  x:  List   tags on folders (only leaf nodes should have tags)
  X:  Delete tags on folders

  y:  List   empty folders (may arise if all children were deleted)
  Y:  Delete empty folders
  
  w:  Write out the bookmarks data to a file
  q:  Quit/exit (Do a Write first!)


Enter option: 
```
## Usage notes
Five modes are supported Listing/Deleting based on:
- A search string in bookmarks or folders
- Duplicate URLs in bookmarks - such as the same URL existing in two or more folders
- Duplicate folder names
- Folders that have tags - there is no value for folders having tags
- Empty folders - which may arise if all bookmarks were deleted from a folder

The Print function prints the full list of bookmarks in folder hierarchical form, listing the ID number and Title of each folder and bookmark.  The `--Print` switch may be used for getting a good visual dump of the bookmarks in less or an editor for reference while using the interactive mode.  The tags on each bookmark are listed as well.

The `--tags <n>` switch prints each tag where the number of bookmarks with that tag >= `<n>`.  Optionally include the `--names` switch to list the bookmark titles/names for each tag.  `--tags 0` is a special case that prints only tags that have exactly one occurrence, which may be useful for finding extraneous/remnant tags usage.

A lower case letter `t/g/d/f/x/y` will list the offenders, while an upper case letter 
`T/G/D/F/X/Y` will allow for selective deletes of the offenders. The lower case List operation need not be run before running the upper case Delete operation.
Delete functions support individual bookmark selection, or all that match the selection mode.
You cannot do any damage to the original bookmark file during a session, so experiment and poke around.

When deleting empty folders you may find that you need to repeat the operation a few times. 
This happens when there are folders within folders that contain no bookmarks.  The lower folder must be deleted before the next higher folder is seen as empty.  Just repeat the folder deletes until the Matches count is 0.

The output file defaults to the Infile + "_OUT", and can be specified during the write operation. You may want to write out intermediate editing results to save work done up to that point. The output format is "pretty printed" json for readability, and is accepted by the xBrowserSync app Restore function via copy/paste.
- **Note** [_may no longer be the case_]: xBrowserSync will allow you to restore to (blast) your current syncID, so you may want to Disable Sync, then create a new sync before restoring the modified bookmarks.
- **NOTE** that tags are not restored by xBrowserSync if sync is disabled.  To recover, simply enable sync and do another restore.



## Known issues:
- This code only works with Python 3.  Development and testing was done on Linux with Python 3.7.3.  Limited testing was done on Windows with Python 3.8.0.


## Version history

- 200330 v1.0   Merged pull request #3 from mblais (add unbuffered getch input; add 'all' option), Reworked --tags and --names switches.
- 191206 v0.3   Added tags dump
- 191205 v0.2   Updated to xbrowsersync v1.5 and Python 3.x ONLY
- 181128 v0.1   New