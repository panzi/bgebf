bgebf
=====

Unpack, list and mount [Beyond Good and Evil](http://ubi.com/US/Games/Info.aspx?pId=9680)
.bf archives.

Basic usage:

	bgebf.py list <archive>                 - list contens of .bf archive
	bgebf.py unpack <archive>               - extract .bf archive
	bgebf.py mount <archive> <mount-point>  - mount archive as read-only file system

The `mount` command depends on the [llfuse](https://code.google.com/p/python-llfuse/)
Python package. If it's not available the rest is still working.

This script is compatible with Python 2.7 and 3 (tested with 2.7.5 and 3.3.2).

File Format
-----------

Extracted from the source of bfbs.c which you can get [here](http://hcs64.com/vgm_ripping.html).

Byte order is little endian and the character encoding seems to be ISO-8859-1.
A value of 0xFFFFFFFF for any field denoting an index is a "null pointer". E.g.
the root directory has a parent directory index of 0xFFFFFFFF. (It might actually
be a signed 32bit integer and the value is -1.)

### Header

	Offset  Size  Type          Description
	     0     4  char[4]       file magic ("BIG\0")
	     4     4  uint32_t      always 0x22
	     8     4  uint32_t      number of files
	    12     4  uint32_t      number of directories
	    16     4  uint32_t      always 0x0
	    20     4  ?             ?
	    24     4  uint32_t      always 0xFFFFFFFF
	    28     4  ?             ?
	    32     4  uint32_t      offset table size
	    36     4  uint32_t      always 0x1
	    40     4  uint32_t      always 0x71003ff9
	    44     4  uint32_t      number of files (again)
	    48     4  uint32_t      number of directories (again)
	    52     4  uint32_t      offset table offset (always 68)
	    56     4  uint32_t      always 0xFFFFFFFF
	    60     4  uint32_t      always 0x0
	    64     4  uint32_t      offset table size - 1 (last used offset table index?)

	filename_table_offset = offset_table_offset + offset_table_size * 8
	dirname_table_offset  = filename_table_offset + offset_table_size * 0x54

### Offset Table Entry

	Offset  Size  Type           Description
	     0     4  uint32_t       file data offset
	     4     4  ?              ?

### File Name Table Entry

	Offset  Size  Type           Description
	     0     4  ?              ?
	     4     4  uint32_t       next index
	     8     4  uint32_t       previous index
	    12     4  uint32_t       directory index
	    16     4  ?              ?
	    20    64  char[64]       file name

### Directory Name Table Entry

	Offset  Size  Type           Description
	     0     4  uint32_t       first file offset
	     4     4  uint32_t       (number of?) sub-directories
	     8     4  uint32_t       next index
	    12     4  uint32_t       previous index
	    16     4  uint32_t       parent index
	    20    64  char[64]       directory name

### File Data Entry

	Offset  Size  Type           Description
	     0     4  uint32_t       size
	     4     4  ?              ?
	     8  size  uint8_t[size]  data

Related Projects
----------------

 * [fezpak](https://github.com/panzi/fezpak): pack, unpack, list and mount FEZ .pak archives
 * [psypkg](https://github.com/panzi/psypkg): pack, unpack, list and mount Psychonauts .pkg archives
 * [unvpk](https://bitbucket.org/panzi/unvpk): extract, list, check and mount Valve .vpk archives
 * [u4pak](https://github.com/panzi/u4pak): unpack, list and mount Unreal Engine 4 .pak archives

BSD License
-----------
Copyright (c) 2014 Mathias Panzenböck

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
