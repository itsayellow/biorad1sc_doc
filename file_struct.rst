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
   | 2-31       | ASCII            | ``Stable File Version 2.0``\ \\r\\n\     |
   |            |                  | <2 spaces>\\r\\n                         |
   +------------+------------------+------------------------------------------+
   | 32-55      | ASCII            | ``Intel Format``\ <10 spaces>\\r\\n      |
   +------------+------------------+------------------------------------------+
   | 56-95      | ASCII            | ``Bio-Rad Scan File - ID``\ <space>\     |
   |            |                  | <17-digit number>                        |
   +------------+------------------+------------------------------------------+
   | 96-135     | ASCII            | <38 spaces>\\r\\n                        |
   +------------+------------------+------------------------------------------+
   | 136-139    | Numbers          | | 0xC8, 0x00, 0x00, 0x00                 |
   |            |                  | | (or uint32 0x000000C8 = 200)           |
   +------------+------------------+------------------------------------------+
   | 140-143    | Numbers          | | 0x03, 0x00, 0x00, 0x00                 |
   |            |                  | | (or uint32 0x00000003 = 3)             |
   +------------+------------------+------------------------------------------+
   | 144-147    | Numbers          | | 0x00, 0x00, 0x00, 0x00                 |
   |            |                  | | (or uint32 0x00000000 = 0)             |
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

Within each Data Block are a series of Data Fields. (See Sections
:ref:`sec-field-structure` and :ref:`sec-field-types` for descriptions)

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

   * Keep reading groups of 7x uint16 values until the end of this Data Block,
     known from reading of the Data Block info fields in the File Header.

3. Parse the next Data Block Header

   * Read 2x uint32 values.
