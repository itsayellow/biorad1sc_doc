.. role:: raw-latex(raw)
   :format: latex
..

File Specification for Bio-Rad 1sc files
========================================

Copyright 2017 Matthew A. Clapp

See end of file for CC-SA information.

The information in this document has been gleaned from many hours of detective
work and examination of 1sc files.

Many thanks to the team of the `Open Microscopy
Environment <https://www.openmicroscopy.org/>`__. Their software package
`Bio-Formats <https://www.openmicroscopy.org/bio-formats/>`__ and their
implementation of `"Bio-Rad Gel 1sc file
format" <https://docs.openmicroscopy.org/bio-formats/5.6.0/formats/bio-rad-gel.html>`__
gave me the start in investigating the structure of this file.

Overall File Structure
----------------------

All known files are little-endian. Seems to be what "Intel Format" at the top
of the file is indicating.

The file is made up of a file header from bytes 0 through 4139, followed by 11
contiguous Data Blocks.

.. table:: **File Header**
   :widths: auto

   +------------+------------------+------------------------------------------+
   | File bytes | Numbers or ASCII | Description                              |
   +============+==================+==========================================+
   | 0-1        | Numbers          | 0xAF, 0xAF ('Magic Number' 1sc file ID)  |
   +------------+------------------+------------------------------------------+
   | 2-31       | ASCII            | ``Stable File Version 2.0``\ \\r\\n      |
   |            |                  | <2 spaces>\\r\\n                         |
   +------------+------------------+------------------------------------------+
   | 32-55      | ASCII            | ``Intel Format``\ <10 spaces>\\r\\n      |
   +------------+------------------+------------------------------------------+
   | 56-95      | ASCII            | ``Bio-Rad Scan File - ID``\ <space>\     |
   |            |                  | <17-digit number>                        |
   +------------+------------------+------------------------------------------+
   | 96-135     | ASCII            | <38 spaces>\\r\\n                        |
   +------------+------------------+------------------------------------------+
   | 136-139    | Numbers          | 0xC8, 0x00, 0x00, 0x00                   |
   |            |                  | (or uint32 0x000000C8 = 200)             |
   +------------+------------------+------------------------------------------+
   | 140-143    | Numbers          | 0x03, 0x00, 0x00, 0x00                   |
   |            |                  | (or uint32 0x00000003 = 3)               |
   +------------+------------------+------------------------------------------+
   | 144-147    | Numbers          | 0x00, 0x00, 0x00, 0x00                   |
   |            |                  | (or uint32 0x00000000 = 0)               |
   +------------+------------------+------------------------------------------+
   | 148-151    | uint32           | Start of Data Block 0 (byte offset from  |
   |            |                  | start of file)                           |
   +------------+------------------+------------------------------------------+
   | 152-155    | uint32           | | <length of file after file header>     |
   |            |                  | | Number of bytes from start of Data     |
   |            |                  |   Block 0 to End Of File.                |
   +------------+------------------+------------------------------------------+
   | 156-159    | Numbers          | 0x00, 0x00, 0x01, 0x00                   |
   |            |                  | (or uint32 0x10000 = 4096)               |
   +------------+------------------+------------------------------------------+
   | 160-379    | Numbers          | | Data Fields describing Data Blocks     |
   |            |                  | | 11x 20-byte Fields                     |
   +------------+------------------+------------------------------------------+
   | 380-4139   | Numbers          | 3760 bytes of 0x00                       |
   +------------+------------------+------------------------------------------+
   | 4140-      | Mixed            | Start of Data Block 0                    |
   +------------+------------------+------------------------------------------+

Within each Data Block are a series of Data Fields. (See Sections **Field
Structure** and **Field Types** for descriptions)

Fields can contain references to other fields, by using a uint32 Data ID to
refer to other fields. Each referenceable field has its own unique Data ID
recorded in its Field Header.

The entire file can be parsed by reading a data format definition of a data
Collection in each even-numbered Data Block, with the actual data in the
following odd-numbered Data Block. The first Field in each odd-numbered Data
Block is always (often?) the root Field of a hierarchical set of data based on
references to other Data Fields inside the same Data Block.

Alternatively, after byte 4140, the entire file can be parsed as a series of
contiguous Data Fields, with special parsing for Field Type 0 (End Of Data
Block). If parsing the entire file at once, (and not each Data Block in
isolation,) one can use the following method when encountering Field Type 0:

1. Parse the End Of Data Block Field

   * Field Type: 0
   * Field Len: 8
   * Field ID: 0

2. Parse the Data Block Footer

   #. Keep reading groups of 7x uint16 values until the end of this Data Block,
      known from reading of the Data Block info fields in the File Header.

3. Parse the next Data Block Header

   #. Read 2x uint32 values.

Data Block Structure
--------------------

Note: Data Block 10, the "Image Data" Data Block, has no Data Block Header, no
Data Block Footer, and no Data Fields. It only consists of image data.

All other Data Blocks follow the structure described below.

Data Block Header
~~~~~~~~~~~~~~~~~

The start of each Data Block starts with 2x uint32 numbers.

The first number is the length in bytes of this Data Block Header and all the
following Data Block fields, (including the last field, Field Type 0.) This
length does **not** include the Data Block Footer.

The second number is currently of unknown significance. It has been observed to
be one of: 1, 2, 4, 7, 8.

+---------+----------+------------------------------------------------+
| Bytes   | Type     | Description                                    |
+=========+==========+================================================+
| 0-3     | uint32   | Data Block Length in Bytes (Header + Fields)   |
+---------+----------+------------------------------------------------+
| 4-7     | uint32   | Unknown (1, 2, 4, 7, or 8)                     |
+---------+----------+------------------------------------------------+

Data Block Fields
~~~~~~~~~~~~~~~~~

Following the bytes of the Data Block Header, the fields inside the Data Block
are parsed contiguously as normal.

The last field of the Data Block fields is Field Type 0. Field Type 0, Field
Len 8 signifies End Of Data Block. This field is only a Field Header--the
length of 8 bytes only allows for the length of a Field Header.

Data Block Footer
~~~~~~~~~~~~~~~~~

The data after this Field Type 0 until the end of the Data Block is the Data
Block Footer.

The footer is a summary of information about the fields seen in this Data
Block. It is composed of groups of 14 bytes. Each group summarizes information
on a particular Field Type. The groups are in the following format:

+---------+----------+-----------------------------+
| Bytes   | Type     | Description                 |
+=========+==========+=============================+
| 0-1     | uint16   | Item 0 Field Type           |
+---------+----------+-----------------------------+
| 2-5     | uint32   | Item 0 Num. Occurrences A   |
+---------+----------+-----------------------------+
| 6-9     | uint32   | Item 0 Num. Occurrences B   |
+---------+----------+-----------------------------+
| 10-13   | uint32   | Item 0 Unknown              |
+---------+----------+-----------------------------+
| .       |          |                             |
+---------+----------+-----------------------------+
| 14-15   | uint16   | Item 1 Field Type           |
+---------+----------+-----------------------------+
| ...     | ...      | ...                         |
+---------+----------+-----------------------------+

"Occurrences A" and "Occurrences B" sum to the total number of occurrences of
the Field Type in the Data Block. They must refer to different types of
occurrences, but in which way is unknown.

The Unknown field may be (?) the number of times a given Field Type has been
referenced in the Data Block.

Field Structure
---------------

Each field in the file is composed of an 8-byte Header, followed by data in the
Payload.

Field IDs can be different for the same string in different files. They are not
consistent across files.

Header
~~~~~~

+----------+---------+---------------+
| Bytes    | Type    | Description   |
+==========+=========+===============+
| 0-1      | uint16  | Field Type    |
+----------+---------+---------------+
| 2-3      | uint16  | Field Length  |
|          |         | in bytes      |
|          |         | (including    |
|          |         | Header        |
|          |         | bytes)Value   |
|          |         | of 1          |
|          |         | indicates     |
|          |         | Field Length  |
|          |         | of 20         |
+----------+---------+---------------+
| 4-7      | uint32  | Field ID      |
+----------+---------+---------------+

Payload
~~~~~~~

+----------------------+-----------------------------------+----------------+
| Bytes                | Type                              | Description    |
+======================+===================================+================+
| 8 - <End Of Field>   | byte or uint16 or uint32 or mix   | Payload Data   |
+----------------------+-----------------------------------+----------------+

Field Types
-----------

Field Referencing Sequence
~~~~~~~~~~~~~~~~~~~~~~~~~~

After the File Header, the basic progression of Fields is as follows:

1. Field Type 102 defining a collection, with a Label string reference and
   reference to a Field Type 101 containing definitions of the data in the
   collection.
2. Field Type 101 defining multiple data items. Each item has a string
   reference serving as a label, the Field Type which would contain the actual
   data, and a corresponding Field Type 100 reference which serves as the Data
   Key to explain the regions of the data. The Field(s) containing the data
   follow this Field, **until the next Field Type 102 is found.** When the next
   Field Type 102 is found, it redefines all info about Data Fields. If Field
   Type 102 is found before the actual data Field Type is found, then the
   actual data does not exist for this item.
3. A series of Field Type 100's, serving as Data Keys for each of the Data
   Items.
4. A series of data container fields, with Field Types greater than 102,
   usually 1000 and above.

This cycle starts over when the next Field Type 102 is encountered.

The Data Blocks come in pairs. Each even-numbered Data Block (starting with 0)
contains field types 102, 101, and 100. These define the structure of the data
following in the next Data Block. The following odd-numbered Data Block
contains the actual data in field types numbered greater than 102.

The exception to the pattern of pairs of Data Blocks is Data Block 10,
containing image data. It has no fields, no previous structure definition, and
only contains raw image data.

NOP Fields
~~~~~~~~~~

+----------------+---------------------------------+---------------------------+----------+
| Field Type     | Contains References to types    | Is Referenced by types    | Notes    |
+================+=================================+===========================+==========+
| 0              | **None**                        | **None**                  | End Of   |
|                |                                 |                           | Data     |
|                |                                 |                           | Blockfie |
|                |                                 |                           | ld\_id   |
|                |                                 |                           | = 0Data  |
|                |                                 |                           | Block    |
|                |                                 |                           | Footer   |
|                |                                 |                           | and next |
|                |                                 |                           | Data     |
|                |                                 |                           | Block    |
|                |                                 |                           | Header   |
|                |                                 |                           | follows. |
+----------------+---------------------------------+---------------------------+----------+
| 2              | **None**                        | 1015                      | nop      |
|                |                                 |                           | field? - |
|                |                                 |                           | payload  |
|                |                                 |                           | is all   |
|                |                                 |                           | 0's,     |
|                |                                 |                           | otherwis |
|                |                                 |                           | e        |
|                |                                 |                           | normal   |
|                |                                 |                           | header   |
+----------------+---------------------------------+---------------------------+----------+

Data Block Info Fields
~~~~~~~~~~~~~~~~~~~~~~

Data Block Info Fields are special fields found only in the File Header. They
define the location and size of the Data Blocks in the file.

Structure
^^^^^^^^^

All Data Block Info Fields have the following structure:

-  **NO** references to other fields
-  **NOT** referenced by other field
-  Field ID = 0
-  Field Len = 20 (bytes 2-3 in header uint16 = 1)

+---------------+-----------------+-------------------------------------------------------+
| Field bytes   | Number Format   | Description                                           |
+===============+=================+=======================================================+
| 0-1           | uint16          | Field Type                                            |
+---------------+-----------------+-------------------------------------------------------+
| 2-3           | uint16          | 0x0001 = 1Field Len of 20                             |
+---------------+-----------------+-------------------------------------------------------+
| 4-7           | uint32          | 0x0000 = 0Field ID of 0                               |
+---------------+-----------------+-------------------------------------------------------+
| 8-11          | uint32          | Data Block startByte offset from start of file.       |
+---------------+-----------------+-------------------------------------------------------+
| 12-15         | uint32          | Data Block lengthNumber of bytes in Data Block.       |
+---------------+-----------------+-------------------------------------------------------+
| 16-17         | uint16?         | Data Block number?(except 11 for Data Block 0 Info)   |
+---------------+-----------------+-------------------------------------------------------+
| 18-19         | uint16?         | Unknown                                               |
+---------------+-----------------+-------------------------------------------------------+

Field Types
^^^^^^^^^^^

+--------------+----------------------------------+
| Field Type   | Notes                            |
+==============+==================================+
| 142          | Data Block 0 info                |
+--------------+----------------------------------+
| 143          | Data Block 1 info                |
+--------------+----------------------------------+
| 132          | Data Block 2 info                |
+--------------+----------------------------------+
| 133          | Data Block 3 info                |
+--------------+----------------------------------+
| 141          | Data Block 4 info                |
+--------------+----------------------------------+
| 140          | Data Block 5 info                |
+--------------+----------------------------------+
| 126          | Data Block 6 info                |
+--------------+----------------------------------+
| 127          | Data Block 7 info                |
+--------------+----------------------------------+
| 128          | Data Block 8 info                |
+--------------+----------------------------------+
| 129          | Data Block 9 info                |
+--------------+----------------------------------+
| 130          | Data Block 10 info(image data)   |
+--------------+----------------------------------+

String Field
~~~~~~~~~~~~

+----------------+---------------------------------+---------------------------+----------+
| Field Type     | Contains References to types    | Is Referenced by types    | Notes    |
+================+=================================+===========================+==========+
| 16             | **None**                        | 100, 101, 102, 131, 1000  | Previous |
|                |                                 |                           | data     |
|                |                                 |                           | fields   |
|                |                                 |                           | referenc |
|                |                                 |                           | e        |
|                |                                 |                           | this via |
|                |                                 |                           | Field    |
|                |                                 |                           | IDNull-t |
|                |                                 |                           | erminate |
|                |                                 |                           | d        |
|                |                                 |                           | string.  |
|                |                                 |                           | (0x00 is |
|                |                                 |                           | always   |
|                |                                 |                           | last     |
|                |                                 |                           | byte of  |
|                |                                 |                           | payload) |
+----------------+---------------------------------+---------------------------+----------+

Data Description Fields
~~~~~~~~~~~~~~~~~~~~~~~

Data Description Fields Hierarchy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In even-numbered Data Blocks, Field Types 102, 101, 100, (and 16) reference
each other as follows:

::

    102 -> 101 -> 100 -> 16
        \-> 16 \-> 16

Field Type 102
^^^^^^^^^^^^^^

Data Collection definition. A **Root Field** of hierarchy.

+--------------+--------------------------------+--------------------------+
| Field Type   | Contains References to types   | Is Referenced by types   |
+==============+================================+==========================+
| 102          | 16, 101                        | **None**                 |
+--------------+--------------------------------+--------------------------+

+---------------+-----------------+--------------------------------------------+
| Field bytes   | Number Format   | Description                                |
+===============+=================+============================================+
| 8-9           | uint16          | Unknown0                                   |
+---------------+-----------------+--------------------------------------------+
| 10-11         | uint16          | Unknown1                                   |
+---------------+-----------------+--------------------------------------------+
| 12-13         | uint16          | Unknown2 (1000)                            |
+---------------+-----------------+--------------------------------------------+
| 14-15         | uint16          | Items in Collection                        |
+---------------+-----------------+--------------------------------------------+
| 16-19         | uint32          | Collection: Reference to Field Type 101    |
+---------------+-----------------+--------------------------------------------+
| 20-23         | uint32          | Label: Reference to Field Type 16 string   |
+---------------+-----------------+--------------------------------------------+

Field Type 101
^^^^^^^^^^^^^^

Data Item definitions.

Every 20 bytes defines a data item (one following data container Field Type)
until end of field.

+--------------+--------------------------------+--------------------------+
| Field Type   | Contains References to types   | Is Referenced by types   |
+==============+================================+==========================+
| 101          | 16, 100                        | 102                      |
+--------------+--------------------------------+--------------------------+

+---------------+-----------------+---------------------------------------------------+
| Field bytes   | Number Format   | Description                                       |
+===============+=================+===================================================+
| 8-9           | uint16          | Item 0 Field Type containing data                 |
+---------------+-----------------+---------------------------------------------------+
| 10-11         | uint16          | Item 0 Unknown0 (4,5,6,7,16,20,21,22,23)          |
+---------------+-----------------+---------------------------------------------------+
| 12-13         | uint16          | Item 0 Unknown1 (1000)                            |
+---------------+-----------------+---------------------------------------------------+
| 14-15         | uint16          | Item 0 Number of regions in data.                 |
+---------------+-----------------+---------------------------------------------------+
| 16-19         | uint32          | Item 0 Data Key: Reference to Field Type 100      |
+---------------+-----------------+---------------------------------------------------+
| 20-23         | uint16          | Item 0 Total bytes in data.                       |
+---------------+-----------------+---------------------------------------------------+
| 24-27         | uint32          | Item 0 Label: Reference to Field Type 16 string   |
+---------------+-----------------+---------------------------------------------------+
| .             |                 |                                                   |
+---------------+-----------------+---------------------------------------------------+
| 28-31         | uint16          | Item 1 Field Type containing data                 |
+---------------+-----------------+---------------------------------------------------+
| ...           | ...             | ...                                               |
+---------------+-----------------+---------------------------------------------------+

Field Type 100
^^^^^^^^^^^^^^

Data Key explaining each Data Item in a collection.

Every 36 bytes is a data region definition, starting at beginning of Field
Payload, until end of field. Field ID references are to String Fields later in
file.

Num Words, Pointer Byte Offset, and Word Size refer to the payload of a future
data container Field Type tied to this key in a Data Item definition in Field
Type 101.

It is possible for total bytes in a payload of a corresponding data container
field to be a multiple of the bytes defined by this Field Type 100. In this
case, the regions defined here would be repeated when parsing the data
container field.

+--------------+--------------------------------+--------------------------+
| Field Type   | Contains References to types   | Is Referenced by types   |
+==============+================================+==========================+
| 100          | 16                             | 101                      |
+--------------+--------------------------------+--------------------------+

+---------------+-----------------+--------------------------------------------------------------+
| Field bytes   | Number Format   | Description                                                  |
+===============+=================+==============================================================+
| 8-9           | uint16          | Region 0 Data Type                                           |
+---------------+-----------------+--------------------------------------------------------------+
| 10-11         | uint32          | Region 0 Index                                               |
+---------------+-----------------+--------------------------------------------------------------+
| 12-15         | uint32          | Region 0 Num Words                                           |
+---------------+-----------------+--------------------------------------------------------------+
| 16-19         | uint32          | Region 0 Pointer Byte Offset                                 |
+---------------+-----------------+--------------------------------------------------------------+
| 20-23         | uint32          | Region 0 Label: Reference to Field Type 16 string            |
+---------------+-----------------+--------------------------------------------------------------+
| 24-27         | uint16          | Region 0 Unknown1                                            |
+---------------+-----------------+--------------------------------------------------------------+
| 28-31         | uint32          | Region 0 Word Size (bytes) **[1]**                           |
+---------------+-----------------+--------------------------------------------------------------+
| 32-33         | uint16          | Region 0 Unknown2                                            |
+---------------+-----------------+--------------------------------------------------------------+
| 34-35         | uint16          | Region 0 Field Type pointed to (if Data Type is reference)   |
+---------------+-----------------+--------------------------------------------------------------+
| 36-39         | uint16          | Region 0 Unknown4a, 4b (ref.-related)                        |
+---------------+-----------------+--------------------------------------------------------------+
| 40-43         | uint16          | Region 0 Unknown5a, 5b (ref.-related)                        |
+---------------+-----------------+--------------------------------------------------------------+
| .             |                 |                                                              |
+---------------+-----------------+--------------------------------------------------------------+
| 44-47         | uint16          | Region 1 Unknown0                                            |
+---------------+-----------------+--------------------------------------------------------------+
| ...           | ...             | ...                                                          |
+---------------+-----------------+--------------------------------------------------------------+

Notes:

**[1]** Frustratingly, it appears that in some files for unknown reasons, the
Region Word Size sub-field can be 0 for all/most/some regions. In this case
word size must be deduced from the Data Type sub-field.

Data Type can be one of the following:

+------------------+--------------------+---------------------+
| Data Type code   | Description        | Word Size (bytes)   |
+==================+====================+=====================+
| 1                | byte               | 1                   |
+------------------+--------------------+---------------------+
| 2                | byte / ASCII       | 1                   |
+------------------+--------------------+---------------------+
| 3                | u?int16            | 2                   |
+------------------+--------------------+---------------------+
| 4                | u?int16            | 2                   |
+------------------+--------------------+---------------------+
| 5                | u?int32            | 4                   |
+------------------+--------------------+---------------------+
| 6                | u?int32            | 4                   |
+------------------+--------------------+---------------------+
| 7                | u?int64            | 8                   |
+------------------+--------------------+---------------------+
| 9                | u?int32            | 4                   |
+------------------+--------------------+---------------------+
| 10               | double (float)     | 8                   |
+------------------+--------------------+---------------------+
| 15               | uint32 Reference   | 4                   |
+------------------+--------------------+---------------------+
| 17               | uint32 Reference   | 4                   |
+------------------+--------------------+---------------------+
| 21               | u?int32            | 4                   |
+------------------+--------------------+---------------------+
| .                |                    |
+------------------+--------------------+---------------------+
| > 21             | ???                | ???                 |
+------------------+--------------------+---------------------+

Data Container Fields
~~~~~~~~~~~~~~~~~~~~~

Data container fields have Field Types greater than 102. (Note: this may not
strictly be true. (?) To be sure treat any Data Field in odd-numbered Data
Blocks as data container fields.)

Each of these contains data, the format of which is determined by the last
Field Type 100 that is paired with them by an item in Field Type 101.

Field Types of data container fields are often but not limited to: 131, 1000,
many numbers greater than 1000.

Part of the data format of data container fields may include references to
other field IDs, allowing a hierarchical structure of data container fields. If
a region Data Type indicates a Reference, but the actual data is 0, then the
region contains no data and should be ignored.

List of Data Blocks
-------------------

Data Block 0
~~~~~~~~~~~~

Defines the data format for Collection "Overlay Header".

Field Types: 16, 100, 101, 102

Possible Data Items and their Regions:

-  OverlaySave

   -  eType
   -  color
   -  where
   -  parentIndex
   -  start
   -  end
   -  startArrow
   -  endArrow
   -  rotationAngle
   -  orientation
   -  runs
   -  alignment
   -  bkgColor
   -  bTransparentBkg
   -  volumeDataPtr
   -  lassoPtr

-  OverImgloc

   -  x
   -  y

-  OverImgbox

   -  first
   -  last

-  OverlaySaveArray

   -  array
   -  avail
   -  used
   -  regressionType

-  OverTextRun

   -  string
   -  font
   -  fontFace
   -  fontSize
   -  color
   -  scriptStyle
   -  isBold
   -  isItalic
   -  isUnderlined

-  OverTextRunArray

   -  array
   -  avail
   -  used

-  OverVolumeData

   -  sumTotal
   -  sumBorders
   -  numPixels
   -  numPixelsBorders
   -  minPixelValue
   -  maxPixelValue
   -  stdDeviation
   -  concentration
   -  type
   -  hasUserLabel
   -  string
   -  overlaySavePtr

-  OverLasso

   -  start
   -  bounds
   -  nsteps
   -  swused
   -  swavail
   -  steps
   -  integden
   -  pixcnt
   -  maxpix
   -  minpix

Data Block 1
~~~~~~~~~~~~

Actual data for Collection "Overlay Header". See Data Block 0 for details on
possible types of data.

Data Block 2
~~~~~~~~~~~~

Defines the data format for Collection "Q1 Description".

Field Types: 16, 100, 101, 102

Possible Data Items and their Regions:

-  Gel

   -  file\_ver
   -  stripe
   -  notes
   -  nt\_used
   -  nt\_avail
   -  stdname
   -  stdunits
   -  stdtype
   -  blotrows
   -  blotcols
   -  smplwidth
   -  bkgden
   -  bkgtype
   -  calcflags
   -  nbacklog
   -  backlog
   -  tdisp\_md
   -  lbkg\_md
   -  lbkg\_disk
   -  lbkg\_window
   -  sensitivity
   -  min\_peak
   -  noise\_filter
   -  shoulder\_sens
   -  size\_scale
   -  normalize
   -  use\_bandlimit
   -  shadow
   -  lbkg\_flags
   -  bandlimit
   -  tolerance
   -  match\_flags
   -  qcused
   -  qcavail
   -  calcurves
   -  qtyunits
   -  vntr\_ambig
   -  flank
   -  repeat
   -  vntr\_flags
   -  sim\_flags
   -  sim\_tolerance
   -  sim\_required
   -  asl\_used
   -  asl\_avail
   -  as\_links
   -  allele\_set\_code
   -  db\_name
   -  db\_path
   -  db\_filename
   -  db\_id
   -  mod\_time
   -  taglist
   -  db\_gelnum
   -  db\_unit
   -  mobilmap
   -  db\_update
   -  db\_type
   -  adb\_gelnum
   -  adb\_unit
   -  adb\_taglist
   -  flags
   -  bstyle
   -  difdsp
   -  lanes
   -  lnused
   -  lnavail
   -  nxties
   -  nyties
   -  nties
   -  ties

-  Stripe

   -  dens
   -  denused
   -  denavail
   -  bkgbox
   -  minimum
   -  average
   -  maximum

-  Lane

   -  name
   -  nyties
   -  crossings
   -  segtrace
   -  segused
   -  segavail
   -  bands
   -  bandused
   -  bandavail
   -  gpk
   -  gaussused
   -  gaussavail
   -  dentrace
   -  stdlanenum
   -  right\_stdlanenum
   -  right\_frac
   -  smplwidth
   -  lanenum
   -  flags
   -  calcflags
   -  sumden
   -  sumd\_bands
   -  lbkg\_disk
   -  lbkg\_window
   -  lbkg\_flags
   -  dtparm
   -  db\_sample
   -  db\_band\_set
   -  db\_standard
   -  dmt\_used
   -  dmt\_avail
   -  db\_mobil
   -  db\_bset\_flags
   -  adb\_band\_set
   -  adb\_sample
   -  lbkg\_md

-  Lane Pointer

   -  lane pointer

-  Trace

   -  dvused
   -  dvavail
   -  dvals
   -  srcstrace
   -  navg
   -  min
   -  max
   -  avg
   -  bkdvals
   -  gaussdvused
   -  gaussdvavail
   -  gaussdvals

-  Tdiag

   -  diag
   -  xaxis
   -  yaxis
   -  data
   -  srctrace
   -  dsttrace
   -  lanenum
   -  datawidth
   -  firstden
   -  max

-  Band

   -  name
   -  sumden
   -  rf
   -  stdval
   -  quality
   -  norm\_den
   -  calnum
   -  qty
   -  this
   -  first
   -  peak
   -  last
   -  maxpix
   -  minpix
   -  lasso
   -  db\_btp\_code
   -  db\_btp\_flags
   -  adb\_btp\_code
   -  adb\_btp\_flags
   -  stdsource
   -  flags
   -  qtysource

-  Band Pointer

   -  band pointer

-  Lasso

   -  start
   -  bounds
   -  nsteps
   -  swused
   -  swavail
   -  steps
   -  integden
   -  pixcnt
   -  maxpix
   -  minpix

-  Band Link

   -  lanenum
   -  Bandnum

-  Imgloc

   -  x
   -  y

-  Imgbox

   -  first
   -  last

-  Band Pointer

   -  unowned band pointer

-  Calcurve

   -  name
   -  desc
   -  from
   -  cbused
   -  cbavail
   -  calbands
   -  ninterp
   -  intps
   -  slope
   -  intercept
   -  corr\_coef
   -  calnum
   -  mcode
   -  model
   -  extrapolate
   -  status
   -  type
   -  named

-  Calcurve Pointer

   -  calcurve pointer

-  Calband

   -  band
   -  measure
   -  qty
   -  reldev
   -  dilution
   -  dilution\_txt
   -  qtysource
   -  relstat

-  Calintp

   -  measure
   -  qty

-  Crosstie

   -  left
   -  ax

-  Crdloc

   -  x
   -  y

-  Stretcloc

   -  a
   -  r

-  MobilTie

   -  rf
   -  mobility
   -  bst\_idx
   -  btp\_code

-  AlleleSetLink

   -  name
   -  id\_safety
   -  allele\_set
   -  als\_item

-  UserDetect

   -  sensitivity
   -  min\_peak
   -  noise\_filter
   -  shoulder\_sens
   -  size\_scale
   -  normalize
   -  use\_bandlimit
   -  shadow
   -  bandlimit

-  BackLog

   -  type
   -  minden
   -  maxden

-  Note

   -  head
   -  tail
   -  text\_start
   -  text
   -  flags

-  tag

   -  pr\_code
   -  vl\_code

-  taglist

   -  used
   -  avail
   -  tags

-  StandardTie

   -  std
   -  mobility

-  MobilMap

   -  lanenum
   -  used
   -  stdties

-  DifDsp Layout

   -  mode
   -  ratio
   -  differ

-  GaussPeak

   -  center
   -  sigma
   -  height
   -  gauerr
   -  lolim
   -  hilim

-  GaussPeak Pointer

   -  gspk pointer

Data Block 3
~~~~~~~~~~~~

Actual data for Collection "Q1 Description". See Data Block 2 for details on
possible types of data.

Data Block 4
~~~~~~~~~~~~

Defines the data format for Collection "DDB Description".

Field Types: 16, 100, 101, 102

Possible Data Items and their Regions:

-  tag

   -  pr\_code
   -  vl\_code

-  taglist

   -  used
   -  avail
   -  tags

-  tag\_value

   -  references
   -  decode

-  tagdef

   -  prompt
   -  references
   -  used
   -  avail
   -  values

-  tagdef\_list

   -  used
   -  avail
   -  tagdefs

-  band

   -  quality
   -  std\_value
   -  norm\_den
   -  btp\_code
   -  flags
   -  peak

-  lane

   -  bands\_used
   -  bands\_avail
   -  bands
   -  sample\_code
   -  bst\_code
   -  flags
   -  dentrace
   -  dmt\_used
   -  dmt\_avail
   -  db\_mobil

-  gel

   -  path
   -  filename
   -  id
   -  name
   -  description
   -  cre\_time
   -  mod\_time
   -  update
   -  lanes\_used
   -  lanes\_avail
   -  lanes
   -  taglist
   -  mobilmap
   -  lanewidth
   -  detection
   -  unit
   -  gidx
   -  stdtype
   -  lbkg\_md
   -  lbkg\_disk
   -  lbkg\_status
   -  layout

-  gel pointer

   -  gel pointer

-  sample

   -  name
   -  cre\_time
   -  description
   -  taglist
   -  idx\_used
   -  idx\_avail
   -  indices
   -  flags

-  sample pointer

   -  sample pointer

-  band\_type

   -  name
   -  btp\_code
   -  index
   -  gidx
   -  lanenum
   -  low\_std
   -  ideal\_std
   -  high\_std
   -  low\_sf
   -  ideal\_sf
   -  high\_sf

-  band set

   -  name
   -  cre\_time
   -  mod\_time
   -  idx\_used
   -  idx\_avail
   -  index
   -  comment
   -  id
   -  tolerance
   -  bst\_idx
   -  bt\_used
   -  bt\_avail
   -  bt\_valid
   -  band\_types
   -  taglist
   -  tagdefs
   -  unit
   -  norm\_btp\_code
   -  gidx
   -  lanenum
   -  method
   -  modified
   -  code\_style
   -  display\_names
   -  report\_names
   -  type
   -  unit\_change
   -  model\_vers

-  band set pointer

   -  band set pointer

-  base

   -  name
   -  description
   -  cre\_time
   -  mod\_time
   -  id
   -  pathname
   -  gels\_used
   -  gels\_avail
   -  gels
   -  gel\_sorting
   -  gel\_sort\_tag
   -  gel\_count
   -  gtpl\_used
   -  gtpl\_avail
   -  gtpl\_count
   -  gel\_templates
   -  smpl\_used
   -  smpl\_avail
   -  samples
   -  sample\_sorting
   -  sample\_count
   -  bst\_used
   -  bst\_avail
   -  band\_sets
   -  bst\_sorting
   -  bst\_count
   -  srch\_used
   -  srch\_avail
   -  srch\_count
   -  searches
   -  tagdef\_list
   -  layouts
   -  units\_used
   -  units\_avail
   -  units
   -  pop\_used
   -  pop\_avail
   -  pop\_count
   -  pop\_links
   -  seg\_map
   -  db\_type

-  layouts

   -  sum
   -  gel\_list
   -  sample\_detail
   -  sample\_list
   -  gel\_detail
   -  bset
   -  srch
   -  odrep
   -  dbp
   -  difdsp
   -  detect

-  gel\_list\_layout

   -  sel\_name
   -  sel\_date\_from
   -  sel\_date\_to
   -  sel\_tag1
   -  sel\_tag2
   -  sort\_by
   -  lst\_pr\_code
   -  dbpos

-  sample\_detail\_layout

   -  tagdefs
   -  dbpos

-  sample\_list\_layout

   -  sel\_tagdef1
   -  sel\_tagdef2
   -  lst\_tagdef1
   -  lst\_tagdef2
   -  sort\_by
   -  dbpos

-  geldet\_layout

   -  gel\_tagdef1
   -  gel\_tagdef2
   -  sample\_tagdef1
   -  sample\_tagdef2
   -  sort\_by
   -  flags
   -  dbpos

-  bset\_layout

   -  unit
   -  tagdefs
   -  default\_bset
   -  lg\_dbpos
   -  sm\_dbpos

-  unit

   -  longname
   -  shortname
   -  unitname
   -  interp
   -  order
   -  flags

-  unit pointer

   -  unit pointer

-  reference lane

   -  gidx
   -  lanenum
   -  bst\_idx

-  search

   -  name
   -  smplname
   -  date\_from
   -  date\_to
   -  taglist
   -  tagdefs
   -  match
   -  ref\_smpl
   -  match\_percent
   -  nlanes
   -  ref\_lanes
   -  srchnum
   -  search\_by
   -  compare
   -  sim\_method
   -  weighting
   -  edited
   -  include
   -  useGaussModelsIfPresent

-  search pointer

   -  search pointer

-  search layout

   -  match\_percent
   -  srchnum
   -  tagdefs
   -  sim\_method
   -  include
   -  weighting
   -  dbpos

-  lane index

   -  gidx
   -  lanenum
   -  bst\_idx

-  pop link

   -  name
   -  plidx
   -  dir\_block
   -  data\_block

-  pop link pointer

   -  poplink pointer

-  segment map

   -  first
   -  nsegs
   -  segs

-  dbp\_pr\_coldata\_fields

   -  type
   -  value

-  pr layout

   -  ref\_lnum
   -  cols\_used
   -  coldata
   -  flags
   -  font

-  sum layout

   -  style
   -  lg\_dbpos
   -  sm\_dbpos

-  imgloc

   -  x
   -  y

-  imgres

   -  x
   -  y

-  ddb position

   -  loc
   -  size
   -  flags

-  dbp ptree layout

   -  dp\_pos
   -  method

-  dbp pca layout

   -  dp\_pos

-  dbp popfrm layout

   -  dp\_pos

-  dbp layouts

   -  popfrm
   -  pr
   -  ptree
   -  pca
   -  irp

-  irp layout

   -  cols\_used
   -  coldata
   -  ref
   -  order
   -  active
   -  style
   -  pg\_layout
   -  show\_btypes
   -  ruler
   -  ref\_lnum

-  odrep layout

   -  od\_types

-  mobilmap

   -  lanenum
   -  used
   -  stdties

-  standardtie

   -  std
   -  mobility

-  DifDsp Layout

   -  mode
   -  ratio
   -  differ

-  detect layout

   -  userdet
   -  screenloc
   -  lane\_width
   -  manual
   -  style
   -  valid

-  userdetect

   -  sensitivity
   -  min\_peak
   -  noise\_filter
   -  shoulder\_sens
   -  size\_scale
   -  normalize
   -  use\_bandlimit
   -  shadow
   -  bandlimit

-  dentrace

   -  dvused
   -  dvavail
   -  dvals
   -  srctrace
   -  navg
   -  min
   -  max
   -  avg
   -  bkdvals
   -  gaussdvused
   -  gaussdvavail
   -  gaussdvals
   -  gaussmax
   -  gaussmin

-  imgbox

   -  first
   -  last

-  db\_mobil.

   -  rf
   -  mobility
   -  bst\_idx
   -  btp\_code

Data Block 5
~~~~~~~~~~~~

Actual data for Collection "DDB Description". See Data Block 4 for details on
possible types of data.

Data Block 6
~~~~~~~~~~~~

Defines the data format for Collection "Audit Trail".

Field Types: 16, 100, 101, 102

Possible Data Items and their Regions:

-  AuditTrail

   -  m\_entries
   -  m\_userPool
   -  m\_descPool
   -  m\_appPool

-  AuditTrailEntry

   -  m\_time
   -  m\_user
   -  m\_description
   -  m\_details
   -  m\_detailX1
   -  m\_detailY1
   -  m\_detailX2
   -  m\_detailY2
   -  m\_version
   -  m\_comment
   -  m\_filter
   -  m\_locked

-  AuditTrailEntryPtr

   -  AuditTrailEntryPtr

-  AuditTrailEntryPtrVector

   -  m\_mmvectorList
   -  m\_mmvectorUsed
   -  m\_mmvectorAvail

-  AuditTrailStringPool

   -  m\_pool

-  AuditTrailStringVector

   -  m\_mmvectorList
   -  m\_mmvectorUsed
   -  m\_mmvectorAvail

-  Imgloc

   -  x
   -  y

-  Imgres

   -  x
   -  y

-  Imgbox

   -  first
   -  last

-  Crdloc

   -  x
   -  y

-  Crdres

   -  x
   -  y

-  Crdbox

   -  first
   -  last

-  Crdscale

   -  x
   -  y

-  ImgState

   -  mincon
   -  maxcon
   -  in
   -  out
   -  low\_frac
   -  high\_frac
   -  state
   -  gamma
   -  aspect

-  Savemap

   -  center
   -  scale

-  CRealPoint

   -  m\_x
   -  m\_y

-  CRealSize

   -  m\_width
   -  m\_height

-  CRealDistance

   -  m\_x
   -  m\_y

-  CRealLine

   -  m\_start
   -  m\_end

-  CRealRect

   -  m\_top
   -  m\_left
   -  m\_right
   -  m\_bottom

-  CImagePoint

   -  m\_x
   -  m\_y

-  CImageSize

   -  m\_width
   -  m\_height

-  CImageDistance

   -  m\_x
   -  m\_y

-  CImageLine

   -  m\_start
   -  m\_end

-  CImageRect

   -  m\_top
   -  m\_left
   -  m\_right
   -  m\_bottom

-  CWindowPoint

   -  m\_x
   -  m\_y

-  CWindowSize

   -  m\_width
   -  m\_height

-  CWindowDistance

   -  m\_x
   -  m\_y

-  CWindowLine

   -  m\_start
   -  m\_end

-  CWindowRect

   -  m\_top
   -  m\_left
   -  m\_right
   -  m\_bottom

-  sm\_string

   -  m\_buffer
   -  m\_length

-  mm\_string

   -  m\_buffer
   -  m\_length

Data Block 7
~~~~~~~~~~~~

Actual data for Collection "Audit Trail". See Data Block 6 for details on
possible types of data.

Data Block 8
~~~~~~~~~~~~

Defines the data format for Collection "Scan Header".

Field Types: 16, 100, 101, 102

Possible Data Items and their Regions:

-  SCN

   -  filevers
   -  creation\_date
   -  last\_use\_date
   -  user\_id
   -  prog\_name
   -  scanner
   -  old\_description
   -  old\_comment
   -  desc
   -  pH\_orient
   -  Mr\_orient
   -  nxpix
   -  nypix
   -  data\_fmt
   -  bytes\_per\_pix
   -  endian
   -  max\_OD
   -  pix\_at\_max\_OD
   -  img\_size\_x
   -  img\_size\_y
   -  min\_pix
   -  max\_pix
   -  mean\_pix
   -  data\_ceiling
   -  data\_floor
   -  cal
   -  formula
   -  imgstate
   -  qinf
   -  params
   -  history
   -  color
   -  light\_mode
   -  size\_mode
   -  norm\_pix
   -  bkgd\_pix
   -  faint\_loc
   -  small\_loc
   -  large\_box
   -  bkgd\_box
   -  dtct\_parm\_name
   -  m\_id32
   -  m\_scnId
   -  m\_imagePK

-  ScnCalibInfo

   -  calfmt
   -  dettyp
   -  isotop
   -  gel\_run\_date
   -  cnts\_loaded
   -  xpo\_start\_date
   -  xpo\_length

-  ScnFormula

   -  type
   -  units
   -  c\_pro
   -  c\_exp

-  ScnImgloc

   -  x
   -  y

-  ScnImgbox

   -  first
   -  last

-  ScnImgState

   -  mincon
   -  maxcon
   -  in
   -  out
   -  low\_frac
   -  high\_frac
   -  state
   -  gamma
   -  aspect

-  ScnQtyInfo

   -  qty\_range
   -  qty\_units
   -  blackIsZero
   -  scanner\_maxpix
   -  scanner\_units
   -  scanner\_bias
   -  scanner\_maxqty
   -  calstep\_count
   -  calstep\_raw
   -  calstep\_qty
   -  calstep\_qty\_offset
   -  gray\_response\_data
   -  gray\_response\_len
   -  gray\_response\_factor

-  ScnCrdloc

   -  x
   -  y

-  ScnCrdres

   -  x
   -  y

-  ScnCrdbox

   -  first
   -  last

-  ScnParams

   -  resolution
   -  scan\_area
   -  exposure\_time
   -  ref\_bkg\_time
   -  gain\_setting
   -  light\_mode
   -  color
   -  intf\_type
   -  size\_mode
   -  imaging\_mode
   -  filter\_name1
   -  filter\_name2
   -  filter\_name3
   -  filter\_name4
   -  filter\_name5
   -  filter\_id1
   -  filter\_id2
   -  filter\_id3
   -  filter\_id4
   -  filter\_id5
   -  laser\_name1
   -  laser\_name2
   -  laser\_name3
   -  laser\_name4
   -  laser\_name5
   -  laser\_id1
   -  laser\_id2
   -  laser\_id3
   -  laser\_id4
   -  laser\_id5
   -  pmt\_voltage
   -  dark\_type
   -  live\_count
   -  app\_name
   -  flat\_field

-  GrayResponseData

   -  GR\_Data

Data Block 9
~~~~~~~~~~~~

Actual data for Collection "Scan Header". See Data Block 8 for details on
possible types of data.

Data Block 10
~~~~~~~~~~~~~

Only image data, no fields

Image data in this block is only pixel data, organized starting from
bottom-left of image to upper-right. The first bytes of this data define the
pixels of the bottom row, from left to right. The next bytes are the
second-to-bottom row from left to right, etc.

All known images are little-endian, 16-bit grayscale. Although the metadata may
define another format. (See e.g. 'Scan Header' -> 'SCN' -> {'endian',
'bytes\_per\_pix', 'data\_fmt' })

--------------

 File Specification for Bio-Rad 1sc Files by Matthew A. Clapp is licensed under
a Creative Commons Attribution-ShareAlike 4.0 International License.
