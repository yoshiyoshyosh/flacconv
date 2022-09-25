# flacconv
flacconv is a 100% POSIX[^1] shell script that recursively converts directories of flac files to opus/mp3.

when invoked with no arguments, it recursively converts the current directory's flac files to opus with a bitrate of 128k, retaining all metadata and not deleting the original flac files

it also has options for you to change the bitrate, use a variable quality for mp3, keep/remove metadata, and parallel processing

## dependencies
- Some implementation of a shell and [POSIX Utilities](https://pubs.opengroup.org/onlinepubs/9699919799/idx/utilities.html) (usually, this is GNU coreutils, which you probably have)
- `flac`
- `lame` (required for mp3, not required otherwise)
- `opus-tools` (required for opus, not required otherwise)
- `id3v2` (optional, for removing invalid ID3 tags in a flac file before converting)
- `curl` (optional, for checking for updates)

## usage
```
flacconv [-hudp3] [-b BITRATE] [-v LEVEL] [-k KEYS] [-r KEYS] [-j THREADS] [--] [DIRECTORY...]
DIRECTORY can be specified multiple times. if omitted, the current directory is used
if encoding to mp3, the only metadata that will be kept is the following:
  TITLE, ARTIST, ALBUM, ALBUMARTIST, DATE, GENRE, TRACKNUMBER, COMMENT, and the cover picture
  
 -h           show script help
 -u           check for updates
 -i           ignore script-stopping warnings
 -d           delete original flac files after transcoding
 -3           switch output filetype to mp3
 -b <BITRATE> output bitrate in kbits (default 128)
              this value is variable for opus & CBR for mp3
 -v <LEVEL>   mp3 only: use specified mp3 variable quality (0-9). integer only
              OVERRIDES -b
 -k <KEYS>    keep specified flac metadata KEYS in output file
              keys can be checked with metaflac --export-tags-to=- FILE
              option argument is a PIPE-separated list of keys to keep, case-insensitive
              (e.g. -k 'artist|title|albumartist|album|date')
              if both -k and -r are not present, all keys are kept.
 -r <KEYS>    remove specified flac metadata KEYS in output file
              cannot be used with -k
              option argument is of the same format as -k
              if set to 'ALL', all keys are removed
 -p           remove embedded picture in output files
 -j <THREADS> use the specified amount of threads for parallel processing
              if omitted, CPU core count will be used
```

## examples
recursively convert a directory of flacs to opus 128k, removing the picture for every one

`flacconv -p /path/to/dir`

recursively convert the current directory to mp3 320k

`flacconv -b 320 -3`

recursively convert current directory to opus 160k, while removing any COMMENT or DESCRIPTION tags

`flacconv -b 160 -r "COMMENT|DESCRIPTION"`

recursively convert current directory to mp3 v0, removing all pictures, and keeping only TITLE, ARTIST, ALBUM, TRACKNUMBER

`flacconv -3 -v 0 -p -k "TITLE|ARTIST|ALBUM|TRACKNUMBER"`

[^1]: tested on dash and yash
