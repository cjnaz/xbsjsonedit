# xbsjsonedit

xbsjsonedit is a tool for working with backup data dumped from the 
[xBrowserSync App](https://www.xbrowsersync.org/) ([GitHub](https://github.com/xBrowserSync)).

xbsjsonedit supports both interactive and command line modes/features.  Interactive mode is targeted at
editing your bookmark content, specifically looking for duplicate bookmarks and errors introduced by earlier versions
of xBrowserSync.  

Command line (CLI) modes support searching and printing select views of your bookmark data.  The `--html` mode outputs a
browser-loadable web page with your bookmarks organized by tag name.  See more info below.

## Notable changes in the latest release
V1.1 200608
- Added `--html` command line mode which generates html output of your tagged bookmarks.
- Changed the command line interface so that the input file defaults to the newest xbs_backup*.json file in your specified
directory (per the JSON_PATH_DEFAULT assignment in the code).  The input file may also be specified using the
`--jsonbackup` command line switch.
- Added `SearchTerm` command line mode feature, which prints matches within tags, and with the `--bookmark` switch prints matches 
within bookmark titles and URLs.


## Usage
```
$ ./xbsjsonedit -h
usage: xbsjsonedit [-h] [--bookmarks] [--print] [--tags TAGS] [--names]
                   [--html HTML] [--jsonbackup JSONBACKUP] [-V]
                   [SearchTerm]

xBrowserSync json backup editor

Usages:
  Command line modes:
    xbsjsonedit news                   prints bookmarks tagged with 'news'
    xbsjsonedit news --bookmarks       prints bookmarks tagged with 'news' and any bookmarks or URLs containing 'news'
    xbsjsonedit --print                prints all bookmarks with their hierarchy
    xbsjsonedit --tags 5               prints only tag names that are used at least 5 times
    xbsjsonedit --tags 5 --names       prints tag names and their bookmarks that are used at least 5 times
    xbsjsonedit --tags 0 --names       prints tag names and their bookmark that are used exactly 1 time
    xbsjsonedit --html <outfile.html>  writes tagged bookmarks to the specified browsable file

  Interactive mode:
    xbsjsonedit                     Enters interactive mode

  The path to the xbs_backup*.json file defaults to the newest in the download directory.  --jsonbackup overrides the default.

positional arguments:
  SearchTerm            Print bookmarks tagged with this text (CLI mode).

optional arguments:
  -h, --help            show this help message and exit
  --bookmarks, -b       Print bookmarks containing or tagged with the SearchTerm (CLI mode).
  --print, -p           Print complete bookmark hierarchy (redirect to less or a file) (CLI mode).
  --tags TAGS, -t TAGS  Print tags list filtered by tags with a minimum <n> number of uses.  n=0 prints only single use tags. (CLI mode)
  --names, -n           Used with --tags to enable printing the titles/names for each tag. (CLI mode)
  --html HTML           Path of html output file.
  --jsonbackup JSONBACKUP, -j JSONBACKUP
                        Path to json backup file. Default is newest version at </mnt/share/Downloads/xbs_backup*.json>.
  -V, --version         Return version number and exit.

```

## Usage notes for Interactive mode
```
$ ./xbsjsonedit

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

```
By default, the most recent `xbs_backup*.json` file in your `JSON_PATH_DEFAULT` directory is opened if `--jsonbackup` is not specified on the command line.  Customize the `JSON_PATH_DEFAULT` Windows and/or Linux settings in the code.

The `p` Print function prints the full list of bookmarks in folder hierarchical form, listing the ID number and Title of each folder and bookmark.

Interactive mode supports five modes of Listing/Deleting based on:
- A search string in bookmarks or folders
- Duplicate URLs in bookmarks - such as the same URL existing in two or more folders
- Duplicate folder names
- Folders that have tags - there is no value for folders having tags
- Empty folders - which may arise if all bookmarks were deleted from a folder

A lower case letter `t/g/d/f/x/y` will list the offenders, while an upper case letter 
`T/G/D/F/X/Y` will allow for selective deletes of the offenders. The lower case List operation need not be run before running the upper case Delete operation.
Delete functions support individual bookmark selection, or all that match the selection mode.
You cannot do any damage to the original bookmark file during a session, so experiment and poke around.

When deleting empty folders you may find that you need to repeat the operation a few times. 
This happens when there are folders within folders that contain no bookmarks.  The lower folder must be deleted before the next higher folder is seen as empty.  Just repeat the folder deletes until the Matches count is 0.

The output file defaults to the Infile + "_OUT", and can be specified during the write operation. You may want to write out intermediate editing results to save work done up to that point. The output format is "pretty printed" json for readability, and is accepted by the xBrowserSync app Restore function via copy/paste.
- **Note** [_may no longer be the case_]: xBrowserSync will allow you to restore to (blast) your current syncID, so you may want to Disable Sync, then create a new sync before restoring the modified bookmarks.
- **NOTE** that tags are not restored by xBrowserSync if sync is disabled.  To recover, simply enable sync and do another restore.

## Usage nodes for Command Line Interface (CLI) mode

By default, the most recent `xbs_backup*.json` file in your `JSON_PATH_DEFAULT` directory is opened if `--jsonbackup` is not specified on the command line.  Customize the `JSON_PATH_DEFAULT` Windows and/or Linux settings in the code.

The `--Print` switch may be used for getting a good visual dump of the bookmarks in less or an editor for reference while using the interactive mode.  (Same output as 'p' print in interactive mode.)

If the optional SearchTerm command line argument is specified, all _tags_ containing the SearchTerm substring are printed, along with the names/titles of the bookmarks that have the matching tags.  If the `--bookmarks` switch is also specified, then the names/titles of bookmarks that have the substring in their name, URL, or tag are also printed. For example, SearchTerm `cam` matches tags `camping` and `webcam`, and several bookmarks:
```
$ ./xbsjsonedit cam --bookmarks
***** Bookmarks tagged with <cam>:
  [camping] (2):
    All Stays camping locations
    Big Bend National Park Texas

  [webcam] (5):
    NAU web cam 1
    ADEQ Webcams
    AZ Game and Fish Webcams
    ADOT traffic cams
    Flagstaff webcams


***** Bookmarks containing or tagged with <cam>:
    Cambria Bicycle Outfitter - We Outfit the world in bicycles and bicycle parts
    Camping World - Home Page
    NAU web cam 1
    Fry's Home Electronics | Computer Parts & Accessories, Software, Games, TVs, Cameras - Frys.com
    Newegg.com - Computer Parts, Laptops, Electronics, HDTVs, Digital Cameras and More!
    ADEQ Webcams
    AZ Game and Fish Webcams
    ADOT traffic cams
    Flagstaff webcams
    All Stays camping locations
    Big Bend National Park Texas
```

The `--tags <n>` switch prints each tag where the number of bookmarks with that tag >= `<n>`.  Optionally include the `--names` switch to list the bookmark titles/names for each tag.  `--tags 0` is a special case that prints only tags that have exactly one occurrence, which may be useful for finding extraneous/remnant tags usage.

The `--html <outfile.html>` switch outputs a web page to the specified file that contains all tags with two (default) or more bookmarks.  `--tags <n>` may be specified to override the default minimum tag usage count.  
- Note that untagged bookmarks, and bookmarks with tags that are used less than the `--tags` count (default 2), are not output to the outfile.html.
- <outfile.html> is statically generated on-demand from a backup file, and thus does track bookmark changes made within xBrowserSync.
- You probably do not want to post this output file on a publicly visible server.
- The `--html` mode/feature is intended as a prototype web page that xBrowserSync may output natively in a future release, so it may not be feature-complete for your needs.


## Known issues:
- This code only works with Python 3.  Development and testing was done on Linux with Python 3.7.3.  Limited testing was done on Windows with Python 3.8.0.


## Version history

- 200608 v1.1   Added cli search and html features, defaulted .json file to be the newest in the download directory.
- 200330 v1.0   Merged pull request #3 from mblais (add unbuffered getch input; add 'all' option), Reworked --tags and --names switches.
- 191206 v0.3   Added tags dump
- 191205 v0.2   Updated to xbrowsersync v1.5 and Python 3.x ONLY
- 181128 v0.1   New