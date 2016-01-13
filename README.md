# kern2D
Basic 2D Geometry Kernel for possible use in ECAD

This project aims to provide a basic 2D geometry kernel which can
be used within an ECAD system to describe PCB pads and traces. The
basic ideas are:

1. all tracks and shapes can be represented by line segments and
circular arc segments, both of which shall be known here as
Segments. There should be no need to define other types of segments
since arbitrary curves can be approximated by circular arc segments
to within typical manufacturing errors.

2. a linear sequence of segments in which the endpoint of a
segment meets an endpoint of the next segment shall be known here
as a Path

3. a closed Path shall be known here as a Loop

4. a PCB Pad's outline can be described by a Loop. For example a
rectangular or trapezoidal pad can be represented by four line
segments; if the user wishes to have filleted corners then the
pad can be represented by a Loop consisting of alternating line
segments and circular arc segments. A circular pad can be
represented by a single closed circular arc.

5. a PCB track segment can be described by a line or arc segment
and an associated width

6. a Fill Region can be described by an arbitrary Loop. The region
can be filled with a Fill Pattern which is itself described by an
arbitrary Loop

Operations: a number of operations must be supported in order to
perform useful tasks such as defining the outline of a track
segment and performing clearance tests.

1. Offset:  This operation can apply to any class of Segment, Path,
or Loop and the result, if one exists, is a Loop. In the case of
a Segment or a Path, only a positive offset yields a result while
for a Loop a positive offset always yields a result while a negative
offset may or may not yield a result. For example, take a Loop
which consists of a single circular arc and imagine a negative offset
applied to the loop. If the offset's magnitude is smaller than the
arc's radius then the result is a circle which is smaller than the
original Loop but if the offset's magnitude is greater or equal to the
circle's radius then there is no result (or more properly, the result
is not a valid 2-dimensional Loop). In practice an Offset operation
is performed when defining the pysical outline of a track segment's
associated copper or when performing clearance tests. Offsets are
also performed in the course of computing a copper fill zone's final
geometry.

2. Union: This operation applies to two loops and could be considered
a case of addition: if two Loops intersect within a non-zero area then
their union is a single Loop, otherwise their Union consists of the
two original Loops.

3. Subtraction: Imagine Loop1 is an outline and Loop2 is a cutout.
That's all I have to say for this note since the possible results
are myriad; however the operation is worth mentioning since it has
many practical uses such as the definition of the final geometry
of a Fill Zone on a PCB

4. Collision: If Loop1 and Loop2 intersect at more than 1 point or
Loop1 is entirely within Loop2 or vice-versa then we have a collision.
A collision test is useful in practice for conducting clearance checks.
For example, take the Loops representing a Pad and a Track (or a Track
and Track or Pad and Pad) on different subnets - call these Lp and Lt.
Take the greater min. clearance requirement for each of those entities
and let's call that C.  To check if the min. clearance has been met we
simply apply offset Lp by C and check for a collision of with Lt.
Alternatively we offset Lt by C and check for a collision with Lp.
Whatever is done in practice depends on how the ECAD wishes to
optimize the operation. For the sake of completeness, a second
alternative exists which is to offset both Lp and Lt by C/2 and check
for a collision but such a strategy always yields a maximum amount
of computations and is therefore an impractical choice.

