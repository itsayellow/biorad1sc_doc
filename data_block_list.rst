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

