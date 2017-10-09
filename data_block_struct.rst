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

.. table:: **Data Block Header**
   :widths: auto

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

.. table:: **Data Block Footer**
   :widths: auto

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
   |         |          |                             |
   +---------+----------+-----------------------------+
   | 14-15   | uint16   | Item 1 Field Type           |
   +---------+----------+-----------------------------+
   | \...    | \...     | \...                        |
   +---------+----------+-----------------------------+

"Occurrences A" and "Occurrences B" sum to the total number of occurrences of
the Field Type in the Data Block. They must refer to different types of
occurrences, but in which way is unknown.

The Unknown field may be (?) the number of times a given Field Type has been
referenced in the Data Block.
