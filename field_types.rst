.. _sec-field-types:

Field Types
-----------

Field Referencing Sequence
~~~~~~~~~~~~~~~~~~~~~~~~~~

After the File Header, the basic progression of Fields is as follows:

#. Field Type 102 defining a Collection, with a Label string reference and
   reference to a Field Type 101 containing definitions of the data in the
   Collection.
#. Field Type 101 defining multiple data items. Each item has a string
   reference serving as a label, the Field Type which would contain the actual
   data, and a corresponding Field Type 100 reference which serves as the Data
   Key to explain the regions of the data. The Field(s) containing the data
   follow this Field, **until the next Field Type 102 is found.** When the next
   Field Type 102 is found, it redefines all info about Data Fields. If Field
   Type 102 is found before the actual data Field Type is found, then the
   actual data does not exist for this item.
#. A series of Field Type 100's, serving as Data Keys for each of the Data
   Items.
#. A series of data container fields, with Field Types greater than 102,
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

.. table:: **NOP Field Types Summary**
   :widths: 1,1,1,4

   +------------+------------+---------------+-------------------------------+
   | Field Type | Contains   | Is Referenced | Notes                         |
   |            | References | by types      |                               |
   |            | to types   |               |                               |
   +============+============+===============+===============================+
   | 0          | **None**   | **None**      | | End Of Data Block           |
   |            |            |               | | field\_id = 0               |
   |            |            |               | | Data Block Footer and next  |
   |            |            |               |   Data Block Header follows.  |
   +------------+------------+---------------+-------------------------------+
   | 2          | **None**   | 1015          | nop field? - payload is all   |
   |            |            |               | 0's, otherwise normal header  |
   +------------+------------+---------------+-------------------------------+

Data Block Info Fields
~~~~~~~~~~~~~~~~~~~~~~

Data Block Info Fields are special fields found only in the File Header. They
define the location and size of the Data Blocks in the file.

.. table:: **Data Block Info Field Type Summary**
   :widths: 1,1,1,4

   +------------+------------+---------------+--------------------------------+
   | Field Type | Contains   | Is Referenced | Notes                          |
   |            | References | by types      |                                |
   |            | to types   |               |                                |
   +============+============+===============+================================+
   | 126, 127,  | **None**   | **None**      | | Field ID = 0                 |
   | 128, 129,  |            |               | | Field Len = 20 (bytes 2-3 in |
   | 130, 132,  |            |               |   header uint16 = 0x0001)      |
   | 133, 140,  |            |               |                                |
   | 141, 142,  |            |               |                                |
   | or 143     |            |               |                                |
   +------------+------------+---------------+--------------------------------+

Structure
^^^^^^^^^

All Data Block Info Fields have the following structure:

.. table:: **Data Block Info Field Structure**
   :widths: auto

   +-------------+---------------+--------------------------------------------+
   | Field bytes | Number Format | Description                                |
   +=============+===============+============================================+
   | 0-1         | uint16        | Field Type                                 |
   +-------------+---------------+--------------------------------------------+
   | 2-3         | uint16        | | 0x0001 = 1                               |
   |             |               | | Field Len of 20                          |
   +-------------+---------------+--------------------------------------------+
   | 4-7         | uint32        | | 0x0000 = 0                               |
   |             |               | | Field ID of 0                            |
   +-------------+---------------+--------------------------------------------+
   | 8-11        | uint32        | | Data Block start                         |
   |             |               | | Byte offset from start of file.          |
   +-------------+---------------+--------------------------------------------+
   | 12-15       | uint32        | | Data Block length                        |
   |             |               | | Number of bytes in Data Block.           |
   +-------------+---------------+--------------------------------------------+
   | 16-17       | uint16?       | | Data Block number?                       |
   |             |               | | (except 11 for Data Block 0 Info)        |
   +-------------+---------------+--------------------------------------------+
   | 18-19       | uint16?       | Unknown                                    |
   +-------------+---------------+--------------------------------------------+

Field Types
^^^^^^^^^^^

.. table:: **Data Block Info Field Types**
   :widths: auto

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
   | 130          | | Data Block 10 info             |
   |              | | (image data)                   |
   +--------------+----------------------------------+

String Field
~~~~~~~~~~~~

.. table:: **String Field Type Summary**
   :widths: 1,1,1,4

   +------------+------------+---------------+--------------------------------+
   | Field Type | Contains   | Is Referenced | Notes                          |
   |            | References | by types      |                                |
   |            | to types   |               |                                |
   +============+============+===============+================================+
   | 16         | **None**   | 100, 101,     | | Previous data fields         |
   |            |            | 102, 131,     |   reference this via Field ID. |
   |            |            | 1000          | | Null-terminated string.      |
   |            |            |               |   (0x00 is always last byte    |
   |            |            |               |   of payload)                  |
   +------------+------------+---------------+--------------------------------+

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

.. table:: **Field Type 102 Summary**
   :widths: 1,1,1,4

   +------------+------------+---------------+-------------------------------+
   | Field Type | Contains   | Is Referenced | Notes                         |
   |            | References | by types      |                               |
   |            | to types   |               |                               |
   +============+============+===============+===============================+
   | 102        | 16, 101    | **None**      | First field of even-numbered  |
   |            |            |               | Data Blocks.                  |
   +------------+------------+---------------+-------------------------------+

.. table:: **Field Type 102 Structure**
   :widths: auto

   +-------------+---------------+--------------------------------------------+
   | Field bytes | Number Format | Description                                |
   +=============+===============+============================================+
   | 8-9         | uint16        | Unknown0                                   |
   +-------------+---------------+--------------------------------------------+
   | 10-11       | uint16        | Unknown1                                   |
   +-------------+---------------+--------------------------------------------+
   | 12-13       | uint16        | Unknown2 (1000)                            |
   +-------------+---------------+--------------------------------------------+
   | 14-15       | uint16        | Items in Collection                        |
   +-------------+---------------+--------------------------------------------+
   | 16-19       | uint32        | Collection: Reference to Field Type 101    |
   +-------------+---------------+--------------------------------------------+
   | 20-23       | uint32        | Label: Reference to Field Type 16 string   |
   +-------------+---------------+--------------------------------------------+

Field Type 101
^^^^^^^^^^^^^^

Data Item definitions.

Every 20 bytes defines a data item (one following data container Field Type)
until end of field.

.. table:: **Field Type 101 Summary**
   :widths: 1,1,1,4

   +------------+------------+---------------+-------------------------------+
   | Field Type | Contains   | Is Referenced | Notes                         |
   |            | References | by types      |                               |
   |            | to types   |               |                               |
   +============+============+===============+===============================+
   | 101        | 16, 100    | 102           | Second field of even-numbered |
   |            |            |               | Data Blocks.                  |
   +------------+------------+---------------+-------------------------------+

.. table:: **Field Type 101 Structure**
   :widths: auto

   +-------------+---------------+--------------------------------------------+
   | Field bytes | Number Format | Description                                |
   +=============+===============+============================================+
   | 8-9         | uint16        | Item 0 Field Type containing data          |
   +-------------+---------------+--------------------------------------------+
   | 10-11       | uint16        | Item 0 Unknown0 (4,5,6,7,16,20,21,22,23)   |
   +-------------+---------------+--------------------------------------------+
   | 12-13       | uint16        | Item 0 Unknown1 (1000)                     |
   +-------------+---------------+--------------------------------------------+
   | 14-15       | uint16        | Item 0 Number of regions in data.          |
   +-------------+---------------+--------------------------------------------+
   | 16-19       | uint32        | Item 0 Data Key: Reference to Field Type   |
   |             |               | 100                                        |
   +-------------+---------------+--------------------------------------------+
   | 20-23       | uint16        | Item 0 Total bytes in data.                |
   +-------------+---------------+--------------------------------------------+
   | 24-27       | uint32        | Item 0 Label: Reference to Field Type 16   |
   |             |               | string                                     |
   +-------------+---------------+--------------------------------------------+
   |             |               |                                            |
   +-------------+---------------+--------------------------------------------+
   | 28-31       | uint16        | Item 1 Field Type containing data          |
   +-------------+---------------+--------------------------------------------+
   | \...        | \...          | \...                                       |
   +-------------+---------------+--------------------------------------------+

Field Type 100
^^^^^^^^^^^^^^

Data Key explaining each Data Item in a Collection.

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

.. table:: **Field Type 100 Summary**
   :widths: 1,1,1,4

   +------------+------------+---------------+-------------------------------+
   | Field Type | Contains   | Is Referenced | Notes                         |
   |            | References | by types      |                               |
   |            | to types   |               |                               |
   +============+============+===============+===============================+
   | 100        | 16         | 101           | After Field Type 101, this    |
   |            |            |               | field type has repeated       |
   |            |            |               | instances until the end of    |
   |            |            |               | even-numbered Data Block.     |
   +------------+------------+---------------+-------------------------------+

.. table:: **Field Type 100 Structure**
   :widths: auto

   +-------------+---------------+--------------------------------------------+
   | Field bytes | Number Format | Description                                |
   +=============+===============+============================================+
   | 8-9         | uint16        | Region 0 Data Type                         |
   +-------------+---------------+--------------------------------------------+
   | 10-11       | uint32        | Region 0 Index                             |
   +-------------+---------------+--------------------------------------------+
   | 12-15       | uint32        | Region 0 Num Words                         |
   +-------------+---------------+--------------------------------------------+
   | 16-19       | uint32        | Region 0 Pointer Byte Offset               |
   +-------------+---------------+--------------------------------------------+
   | 20-23       | uint32        | Region 0 Label: Reference to Field Type    |
   |             |               | 16 string                                  |
   +-------------+---------------+--------------------------------------------+
   | 24-27       | uint16        | Region 0 Unknown1                          |
   +-------------+---------------+--------------------------------------------+
   | 28-31       | uint32        | Region 0 Word Size (bytes)                 |
   |             |               | (**or 0x00000000**) [#region_word_size]_   |
   +-------------+---------------+--------------------------------------------+
   | 32-33       | uint16        | Region 0 Unknown2                          |
   +-------------+---------------+--------------------------------------------+
   | 34-35       | uint16        | Region 0 Field Type pointed to (if Data    |
   |             |               | Type is reference)                         |
   +-------------+---------------+--------------------------------------------+
   | 36-39       | uint16        | Region 0 Unknown4a, 4b (ref.-related)      |
   +-------------+---------------+--------------------------------------------+
   | 40-43       | uint16        | Region 0 Unknown5a, 5b (ref.-related)      |
   +-------------+---------------+--------------------------------------------+
   |             |               |                                            |
   +-------------+---------------+--------------------------------------------+
   | 44-47       | uint16        | Region 1 Unknown0                          |
   +-------------+---------------+--------------------------------------------+
   | \...        | \...          | \...                                       |
   +-------------+---------------+--------------------------------------------+

.. [#region_word_size] Frustratingly, it appears that in some files for unknown
   reasons, the Region Word Size sub-field can be 0 for all/most/some regions.
   In this case word size must be deduced from the Data Type sub-field.

Data Type can be one of the following:

.. table:: **Field Type 100 Region Data Types**
   :widths: auto

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
   |                  |                    |                     |
   +------------------+--------------------+---------------------+
   | \> 21            | \???               | \???                |
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
