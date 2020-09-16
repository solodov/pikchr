# Differences Between Pikchr And Legacy-PIC

Pikchr is mostly compatible with legacy PIC in the sense that it will
run most of the example scripts contained in the
[original technical report on PIC by BWK][bwk] with little to no change.
Nevertheless, some features of legacy PIC have been omitted, and new
features have been added.  This article attempts to highlight the
important differences.

[bwk]: /uv/pic.pdf

Pikchr is implemented from scratch, without reference to the original
PIC code, and without even access to a working version of legacy PIC
with which to perform experiments.  The syntax implemented by Pikchr
is based solely on the descriptions in the [BWK tech report][bwk] which was
intended as a user manual, not a precise description of the language.
Consequently, some details of Pikchr may differ from PIC without our
even being aware of it.  This document tries to list the differences
that we know of.  But there are likely omissions.

## Designed for the Web

Pikchr is designed to be embedded in Markdown and generates SVG
output which blends with the HTML generated by Markdown.  It is
intended for use in software development and software project
management systems for the 2020s and beyond.

PIC was designed to be embedded in [troff][troff] - an historically
significant but now obsolete markup language developed at Bell Labs
in the late 1970s and early 1980s.
PIC could embed troff markup in the middle of
a drawing, a capability omitted from Pikchr (obviously).

[troff]: https://en.wikipedia.org/wiki/Troff

## New Object Types

Pikchr supports serveral new object types that were unavailable
in PIC.

~~~ pikchr indent
oval "oval"
move
cylinder "cylinder"
move
file "file"
move
dot "  dot" ljust
~~~

Additional object types may be added in subsequence versions of Pikchr.

## Units Other Than Inches

PIC operated purely in inches.  Pikchr allows you to attach a
units designator on numeric literals so that distances can be easily
expressed in other units.  For example, you can write "`2.3cm`" to
mean 2.3 centimeters.  This is easier and more intuitive than writing
something like
"`2.3*2.54`".  Pikchr still does all of its calculations in inches,
internally.  The "cm" suffix is actually part of the numeric literal
so that "`2.3cm`" is really just an alternative spelling for "`5.842`".

Units supported by Pikchr include:

  *  `cm` &rarr; centimeters
  *  `in` &rarr; inches (the default)
  *  `mm` &rarr; millimeters
  *  `pc` &rarr; picas
  *  `pt` &rarr; points
  *  `px` &rarr; pixels

Because the units are part of the numeric literal,
the unit designator cannot be separated from the number by whitespace.
Units only apply to numeric literals, not to expressions.


## New Uses For "`radius`":

A positive "`radius`" attribute on "`box`" items causes the box
to be displayed with rounded corners:

~~~ pikchr indent
box rad 15px "box" "radius 15px"
~~~

Similarly a "`radius`" value on a "`line`" or "`arrow`" with
multiple segments rounds the corners:

~~~ pikchr indent
arrow rad 10px go heading 30 then go 200% heading 175 \
  then go 150% west "arrow" below "radius 10px" below
~~~

## The "`color`" and "`fill`" attributes

Any object can have a "`color`" attribute to set its foreground
color and a "`fill`" attribute to set its background color.  The
default "`color`" is black and the default "`fill`" is "None".

~~~ pikchr indent
boxrad = 12px
box color blue "color blue"
move
box fill lightgray "fill lightgray"
move
box color white fill blue "color white" "fill blue"
~~~

## The "`thickness`" attribute

The new "`thinkness`" attribute specifies the stroke-width.  You can
also use attributes "`thick`" and "`thin`" to increase or decrease the
stroke-width in increments.

~~~ pikchr indent
boxrad = 12px
box thin "thin"
move
box "(default)" italic
move
box thick "thick"
move
box thick thick "thick" "thick"
~~~

## Enhanced ability to control text alignment and display

There are new modifiers for text labels:

~~~ pikchr indent
box "bold" bold "italic" italic "big" big "small" small fit
line from 1cm right of previous.se to 3cm right of previous.ne \
   "aligned" above aligned
~~~

In addition, the attribute "`fit`" adjusts the width and height of
box-like objects to snuggly surround their text labels.

## Change numeric property values by a percentage

You can change the value of a numeric attribute by a percentage,
rather than having to specify a particular value:

~~~ pikchr indent
box "default" italic "box" italic
move
box "width 150%" width 150%
move
box "wid 75%" wid 75%
~~~

## The "`chop`" attribute works very differently

The "`chop`" attribute is completely redesigned.  It takes no
argument and can only appear once.  If "`chop`" is specified on
a line (or arrow or spline) then end-points of the line that
would have landed on the center of a box-like object (box,
circle, cylinder, ellipse, file, or oval) are shortened to
land exactly on the border of that object.  

~~~ pikchr indent
file "A"
cylinder "B" at 5cm heading 125 from A
arrow <-> from A to B chop "from A to B chop" aligned
~~~

## The "`same as` *object*" construct

## New ways to discribe line paths

  *  **go** *distance* **heading** *compass-angle*
  *  **go** *distance* *compass-point*
  *  **go** *direction* **until even with** *place*
  *  **close**

## New syntax to describe positions

  *  *distance* **above** *position*
  *  *distance* **left of** *position*
  *  *distance* **heading** *compass-angle* **from** *position*
  *  *nth* **vertex of** *line-object*



## Other miscellaneous new features

### New ways to identify prior objects

  * **previous**
  * **first**
  * Name objects by their string labels

### Support for C and C++ style comments

### Variable names can start with "`$`" or "`@`"

### New assignment operators for variables

  *  +=
  *  -=
  *  *=
  *  /=

### "`invisible`" can optionally be spelled out

### Identify text objects with the keyword "`text`"

### New variables

  *  margin
  *  leftmargin

## iscontinued Features

Pikchr deliberately omits some features of legacy PIC for security
reasons.  Other features are omitted for lack of utility

### Omit "`sh`" and "`copy`" statements.

The "`sh`" command provided the script the ability to run arbitrary
shell commands on the host computer.  Hence "`sh`" was just a built-in
[RCE vulnerability].  Having the ability to run arbitrary shell
commands was a great idea when you were building a phototypestting
system Bell Labs Version-III Unix running on a dedicated PDP/11 in
1982.  But it has no place in modern web-facing applications.  We
stay as far away from that stuff as we can.

[rce]: https://en.wikipedia.org/wiki/Arbitrary_code_execution

The "`copy`" command is similar.  It inserts the text of arbitrary
files on the host computer into the middle of the PIC-script.

### Omit "`for`" and "`if`" statements.

Pikchr omits all support for branching and looping.  Each Pikchr
graphic object maps directly into a single graphic object in the
output.  This is a choice made to enhance the security and safety
of Pikchr (without branching or looping, there is less opportunity
for mischief) and to keep the language simple and accessible.

If you need machine-generated code, employ a separate script
language like Python or TCL to generate the Pikchr script for
you.  Then you can employ all the branching and looping you want.
We just don't want to build that into a language that is openly
accessible to random passers-by on the internet.

### Omit macros



### Omit the built-in "`sprintf()`" function

The sprintf() function has well-known security concerns, and we
do not want to make potential exploits accessible to attackers.
Futhermore, the sprintf() is of little to no utility in a Pikchr
script that lacks loops.  A secure version of sprintf() could be
added to Pikchr, but doing that would basically require recoding
a security sprintf() from from scratch.  It is safer and easier
to simply omit it.

### Omit "`{...}`" subblocks

The "`[...]`" style subblocks are supported and they work just as well.