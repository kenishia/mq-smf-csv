# mq-smf-csv
Simple formatter for MQ's SMF records to assist with import to spreadsheets

README for mqsmfcsv
===================

This package has been created to simplify the job
of doing your own analysis of MQ SMF records from a z/OS system,
by making it easy to import formatted records into
a database or spreadsheet.

The program takes the data from a z/OS file, and runs on
a Windows or Unix system, creating standard CSV
(comma-separated value) output files.

Unlike packages such as SupportPac MP1B, there is no interpretation
at all of the SMF data. That can be done by your own spreadsheet or
database programs and macros.

This makes the code for the program
very easy to understand; all the structures in the SMF records
are printed directly.


Usage
=====
Collect the MQ SMF data in the usual way.
Dump that data to a z/OS file using a tool such as ...
Then download that file to

The output files are written to a directory that you name
on the command line. There is one file for each type of
element - QPST, WTID etc. The files contain a line of
column headers, followed by the actual data with each
field separated by a comma. Spreadsheets such as LibreOffice
or Excel can then import the data directly.

No attempt is made to explain the column headers; these come
straight from the field names. More information can be found in
the csqdsmfc.h header file or the product documentation.

Date and time are split into separate columns; the time
includes a microsecond portion which is separated using ',' as using '.'
as the decimal point seems to confuse the automatic importers on
several spreadsheets. There is no information directly available about
timezone offsets, so it is possible that the timestamps could be either
GMT (CUT) or localtime, depending on system configurations.

Durations are split into separate columns for
seconds and microseconds. This is shown in the column headings.

Options
=======

  printf("Usage: mqsmfcsv [-o <output dir>] [-a] [ -d <level> ]\n");
  printf("         [ -i <input file> [-m <max records>] [-r ] [-t <ticker>]\n");
  printf("  -a Append to existing files if they exist. Default is overwrite\n");
  printf("  -d <level> Debug by dumping binary records (level = 1 or 2)\n");
  printf("  -i <Input file>  Default is to read from stdin\n");
  printf("  -m <Max records> End after formatting M records. Default to process all\n");
  printf("  -o <Directory>   Where to put output files\n");
  printf("  -r               Do not print '=' on numeric-looking strings\n");
  printf("  -t <Ticker>      Print progress message every T records\n");

Design and Development
======================

Binaries are provided for some platforms. For other
platforms, you will need to compile the code yourself.
Familiarity with Makefiles and compiling C programs is
assumed.

First, you need to get a copy of the MQ product-provided
header file on z/OS that defines the structures for SMF
elements. This is called csqdsmfc.h and can be found on your
z/OS installation. Download a copy of that to your system.

Using the provided Makefiles as a template, set the
appropriate compilation options for your platform.
For Unix platforms, the "M" script may be a convenient
way to override options in the Makefile, as most Unix
platforms are likely to be very similar in how to build
programs.

The Makefiles take the z/OS header file and make some
minor transformations so that it is suitable for
compiling on other platforms.

Note that the mqsmfcsv program needs to be compiled with
fixed datatype sizes - in particular, "int" is 32-bit, and
"long long" is used to represent a 64-bit value. Generally
this means that the program itself is compiled as 32-bit.

The current version of this program was developed based on the
MQ V8 SMF structures; it will run against data
generated by older queue managers
but will not compile if an older version of csqdsmfc.h is used.

Each data structure is formatted by its own function, contained in
the print*.c modules. Most of these are very straightforward to
understand. Some common macros defined in mqsmf.h make it
easy to add each field from the structure; they also simplify handling
common fields that need to be printed on every type of SMF element.

A few special cases exist, such as handling bitfields or arrays. But
again, the code where these are used ought to be obvious, and can be
used as strategies for handling new types of SMF element.

Sample data
===========
The package includes a tiny amount of sample data that can be used to
demonstrate the formatting.



downloading ...


Sequential, Variable Length Records

The following is a sample FTP script to retrieve (or GET) a sequential file with variable-length records from a Mainframe system to a Windows system. This FTP script is for using at the client or Windows system.

userid
password
CD ..
PWD
BINARY
QUOTE SITE RDW
GET mainframe.dataset.name drive:\directory\filename.ext
QUIT

The file must be transferred in BINARY. This will maintain the EBCDIC-encoding and the mainframe
numeric formats for packed-decimal, binary and zoned-decimal data.

One of the following FTP statements must be used prior to a GET or PUT statement in order to
download the Record-Descriptor-Word (RDW) and the possible Block-Descriptor-Word (BDW).  The BDW and
RDW are in binary format so it is critical to download in binary even if  the records in the file
are all text strings. Prior to a GET use one of the following statements.
QUOTE SITE RDW
   or
LITERAL SITE RDW

If the FTP process is to be executed on the mainframe the GET statement will need to be replaced
with a PUT and the QUOTE or LITERAL statement will need to be replaced with the following statement.

LOCSITE RDW

Once the file is transferred it is a mirror of the mainframe format with EBCDIC-encoding.  Each
record is preceded by the Record Descriptor Word (RDW) and possible Block Descriptor Word (BDW) and
must be converted to a Micro Focus formatted sequential file with variable length records. This
process is described in the following sections of this document.
