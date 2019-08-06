# scan-public-directory 

`scan-public-directory` is a tool to extract links from a specified public directory returned by a web server.

It permits :
- to specify filters to accept or reject links
- to define and call a command on each selected link (like wget)

## page source

![capture](capture.jpg?raw=true)

## command result

```
$ ./scan-public-directory url http://example.com/images/ 
2009-02-01 11:13|2.4M|http://example.net/images/02-09.jpg
2010-11-03 10:36|3.4M|http://example.net/images/021110.jpg
2007-03-01 08:21|4.1M|http://example.net/images/03-07.jpg
```

## configuration file

A configuration file `.spdrc` is created on the first program execution.

The configuration file defines the regexp to search after cleaning lines.

You can define new formats to detect and extract links.

## usage 
           
The program takes `url` or `file` as the first parameter.                    
                    
```                 
usage: scan-public-directory url [-h] [--max-depth LEVELS]
                                 [--min-depth LEVELS] [--min-size SIZE]
                                 [--max-size SIZE] [--accept LIST]
                                 [--reject LIST] [--verbose]
                                 [--after DATETIME] [--before DATETIME]
                                 [--lines LINES] [--banner] [--print FORMAT]
                                 [--exec COMMAND]
                                 URL

positional arguments:
  URL                 The url where performing operation

optional arguments:
  -h, --help          show this help message and exit
  --max-depth LEVELS  Descend at mots levels
  --min-depth LEVELS  Ignore file links at levels less than levels
  --min-size SIZE     Ignore file with size less than the specified size
  --max-size SIZE     Ignore file with size greater than the specified size
  --accept LIST       Accept only the files with the specified file name
                      suffixes or patterns (comma separated list). If any of
                      the wildcard characters, *, ?, [ or ], appear in an
                      element of the list, it will be treated as a pattern,
                      rather than a suffix
  --reject LIST       Reject the files with the specified file name suffixes
                      or patterns (comma separated list). If any of the
                      wildcard characters, *, ?, [ or ], appear in an element
                      of the list, it will be treated as a pattern, rather
                      than a suffix
  --verbose           Display more messages
  --after DATETIME    Reject the files with date/time before the specified
                      datetime (YYYY-MM-DD hh:mm)
  --before DATETIME   Reject the files with date/time after the specified
                      datetime (YYYY-MM-DD hh:mm)
  --lines LINES       Select specified lines matching all filters (N, N-, N-M,
                      -M)
  --banner            Display banner
  --print FORMAT      Print the given string replacing patterns {index} {date}
                      {size} {url} by their respective values
  --exec COMMAND      Execute the given shell command (ex: echo "#{index}
                      {date} // {size} // {url}"). If command starts with : it
                      is considered as an alias
```

## examples of use

```bash
$ ./scan-public-directory url http://example.com/images/ | tee photo.txt
2009-02-01 11:13|2.4M|http://example.net/images/02-09.jpg
2010-11-03 10:36|3.4M|http://example.net/images/021110.jpg
2007-03-01 08:21|4.1M|http://example.net/images/03-07.jpg
``` 

```bash
$ ./scan-public-directory file photo.txt --accept '*-0*' --before '2015-12-25 19:34' --max-size '3M'
2009-02-01 11:13|2.4M|http://example.net/images/02-09.jpg
``` 

If necessary, after filtering, you can call a shell command (see the `--exec` option in the usage)


## Configuration file sample

```bash
$ cat ~/.spdrc 
dir_regex: '<a href="([^"]*/)">[^<]*</a>'
a_regex: '<a href="([^"]*[^\/])">[^<]*</a>'
clean_line:
  - "\\s*<img[^>]+>\\s*"
  - "\\s*<a[^>]+>[^<]*</a>\\s*"
  - "\\s*<td[^>]*>\\s*"
  - "\\s*<tr[^>]*>\\s*"
  - "\\s*</td>\\s*"
  - "\\s*</tr>\\s*"
  - "&nbsp;"
formats:
  - "DD-MMM-YYYY HH:mm"
  - "YYYY-MMM-DD HH:mm"
  - "M/D/YYYY h:mm A"
  - "YYYY-MM-DD HH:mm"
  - "dddd, MMMM DD, YYYY h:mm A"
a2strptime:
  'dddd': '%A'
  'ddd': '%a'
  'DD': '%d'
  'D': '%d'
  'MMMM': '%B'
  'MMM': '%b'
  'MM': '%m'
  'M': '%m'
  'YYYY': '%Y'
  'HH': '%H'
  'H': '%H'
  'hh': '%I'
  'h': '%I'
  'A': '%p'
  'a': '%p'
  'mm': '%M'
  'm': '%M'
aliases:
  :wget: "wget -c '{url}'"
```
