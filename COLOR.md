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

# Colour Specification: Price Residual Scatterplot

## Variable
price_resid

## Type
Quantitative, signed, with a meaningful midpoint at zero.

## Colormap Family
Diverging, because price_resid contains negative and positive values relative to a meaningful zero.

## Domain and Anchor
Observed range: -1.5083 to +1.3379.

m = max(abs(-1.5083), abs(1.3379)) = 1.5083

Symmetric domain: [-1.5083, 0, +1.5083]

The neutral colour must be anchored at exactly zero.

## Legend
The legend must:
- Show the diverging colour scale.
- Mark zero clearly as the neutral midpoint.
- Label negative values as "Lower price than predicted."
- Label positive values as "Higher price than predicted."
- Identify the values as log-price residuals.

## Check
- Verify that the scale uses [-1.5083, 0, +1.5083].
- Confirm the neutral colour corresponds to zero.
- Check the legend labels and direction.
- Test deuteranopia, protanopia, and greyscale.

At a given carat value, diamonds priced above the model's prediction appear higher on the scatterplot in orange, while diamonds priced below the prediction appear lower in purple, with near-white points representing prices close to the prediction.
