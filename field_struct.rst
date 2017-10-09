.. _sec-field-structure:

Field Structure
---------------

Each field in the file is composed of an 8-byte Header, followed by data in the
Payload.

Field IDs can be different for the same string in different files. They are not
consistent across files.

Header
~~~~~~

.. table:: **Field Header**
   :widths: auto

   +----------+---------+-------------------------------------------+
   | Field    | Type    | Description                               |
   | Bytes    |         |                                           |
   +==========+=========+===========================================+
   | 0-1      | uint16  | Field Type                                |
   +----------+---------+-------------------------------------------+
   | 2-3      | uint16  | | Field Length in bytes (including Header |
   |          |         |   bytes)                                  |
   |          |         | | Value of 1 indicates Field Length       |
   |          |         |   of 20                                   |
   +----------+---------+-------------------------------------------+
   | 4-7      | uint32  | Field ID                                  |
   +----------+---------+-------------------------------------------+

Payload
~~~~~~~

.. table:: **Field Payload**
   :widths: auto

   +---------------------+----------------------------------+----------------+
   | Field Bytes         | Type                             | Description    |
   +=====================+==================================+================+
   | 8 - <End Of Field>  | | byte or                        | Payload Data   |
   |                     | | uint16 or                      |                |
   |                     | | uint32 or                      |                |
   |                     | | mix                            |                |
   +---------------------+----------------------------------+----------------+
