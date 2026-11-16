# Colour Specification: Clarity Scatterplot

## Variable
clarity

## Type
Ordinal, with 8 ranked grades:
I1 < SI2 < SI1 < VS2 < VS1 < VVS2 < VVS1 < IF

## Colormap Family
Sequential blue ramp, because clarity is an ordered variable. Increasing darkness should represent increasing clarity.

## Domain and Anchor
I1 (worst) to IF (best).
The lightest shade represents I1, and the darkest shade represents IF.

## Legend
Display all 8 clarity grades in the correct rank order, from worst to best. Clearly label the lowest and highest clarity grades.

## Check
The initial chart uses a sequential blue ramp, and the legend follows the correct rank order.

However, it is difficult to distinguish all 8 grades in the scatterplot. Only around 3 distinct shades are easily noticeable because of the low opacity and overlapping points.

Greyscale and colour-vision simulation checks are still pending.
