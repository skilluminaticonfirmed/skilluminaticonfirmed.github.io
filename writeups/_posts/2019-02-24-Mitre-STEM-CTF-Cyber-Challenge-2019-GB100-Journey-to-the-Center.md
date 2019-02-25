---
layout: single
title: "MITRE STEM CTF Cyber Challenge 2019 - Journey to the Center of
the File (Grab Bag 100pts)"
date: 2019-02-24
---

Contestant is provied a zip file that contains a file called 'flag'.

| filename                                                                    |
|-----------------------------------------------------------------------------|
|gb100-1-8a79568ebe1483695100d6a04525a7282673746b2c9d5e70b2f1729c42a85d42.zip |

|hash                             |
|---------------------------------|
|528dcf2f5287cab9e13849a9b7e1577f |




## Initial Recon

The *flag* file provided to us does not have an extension nor is any indication made as to
what type of file it is. The [file(1)](https://linux.die.net/man/1/file) utility can be used
to attempt to determine the file type based on magic or header values present..


```bash
$ file flag
flag: bzip2 compressed data, block size = 400k
```


## OK, so its a bzip2 archive, just unpack it...

Upon unpacking the archive using [bzip2(1)](https://linux.die.net/man/1/bzip2) we are greeted
with another archive, this time making use of [Zip](https://en.wikipedia.org/wiki/Zip_(file_format)) 

```
$ bunzip2 flag
bunzip2: Can't guess original name for flag -- using flag.out
$ file flag.out
flag.out: Zip archive data, at least v2.0 to extract
```

Unzipping this archive using [unzip(1)](https://linux.die.net/man/1/unzip) gives us another bzip2 file.

```bash
$ unzip flag.out
Archive:  flag.out
  inflating: flag
$ file flag
flag: bzip2 compressed data, block size = 400k
```

Repeating this process a bit more reveals the following two additional utilities are required to successfully
decompress what we believed to be all the remaining layers.

* [gzip(1)](https://linux.die.net/man/1/gzip)
* [base64(1)](https://linux.die.net/man/1/base64)


## Automating the descent into madness

Manually traversing these archives turned out to be a huge waste of time so we whipped up a quick script to
process the files for us. Luckily we were very familiar with the command line arguments of these utilities at
this point having executed them *too many* times.


```python
import os
import magic
import sys

def unzip(filename, newfile):
    os.system("unzip -p %s > %s" % (filename, newfile))
    return newfile

def gunzip(filename, newfile):
    os.system("gzip -d -c %s > %s" % (filename, newfile))
    return newfile

def bunzip2(filename, newfile):
    os.system("bzip2 -d -c %s > %s" % (filename, newfile))
    return newfile

def base64(filename, newfile):
    os.system("base64 -d %s > %s" %( filename, newfile))
    return newfile


##
# determine archive type

def do_unpack(filename, newfile):
    with magic.Magic() as m:
        s = m.id_filename(filename)
    if 'gzip' in s:
        gunzip(filename, newfile)
    elif 'bzip2' in s:
        bunzip2(filename, newfile)
    elif 'Zip' in s:
        unzip(filename, newfile)
    elif 'with no line terminators' in s:
        base64(filename, newfile)

    else:
        print "ERROR NO MATCH: %s" % (s)
        sys.exit()


if __name__ == "__main__":
    do_unpack("flag", "output.2")

    for i in range(0,498):
        if i%2:
            filename = "output.1"
            newfile = "output.2"
        else:
            filename = "output.2"
            newfile = "output.1"

        print "Current iterations: %d\
                  -- do_unpack(\"%s\", \"%s\")" %
               (i, filename, newfile)
        do_unpack(filename, newfile)

```


Initially we had set the maximum iterations to a much higher value but our script ended up truncating the flag.
So we then set the max iterations to 498, one before the flag was truncated according to terminal scrollback. This
could've been done using a seperate case for a text file without 'really long lines' according to magic string.

As you can see this was not meant to be done manually and the challenge must be solved using a script or some type
of automation.
