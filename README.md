# binaryclustering
####################
This R script contains code to take a spatially-random dummy variable, the nX1 vector denoted "y" (containing k 1's and n-k zeroes), and transforms it into an nX1 vector "z" (also containing k 1's and n-k zeroes) using an nXn spatial weights matrix, "W" (non-standardized binary, indicating which "row" is a neighbor of which "column"), and the spatial autocorrelation coefficient, "rho".
The method involves some nontrivial matrix algebra, series representations of functions, and partial derivatives. For more detail, see the "MESS" article by LeSage & Pace (2007).
User inputs the random vector (y), the degree of autocorrelation (rho), and the spatial weights matrix (W).
First, a numerical approximation to the usual nXn spatial multiplier, "S", is generated using sparse matrices in R (see "sparseMatrix").
Next, S is then right-multiplied by y to yield the nXn spatial effects matrix Sy, and the total effects for each of the n observations is computed as the rowSums[Sy] = z.
Finally, the appropriate quantiles are computed for y (that is, determine the size of k and force the same k on z) and projected onto z. 
#####################
Computational notes:
1.) The MESS method for computing the spatial multiplier uses q=16 terms, which is only a good approximation for a rho at most 0.95. Many more terms in the power serires need to be added for very large values of rho.
2.) Larger values of rho (above 0.5) tend to confuse the quantile functions in R. For example, when rho>0.8, the k_y could be 100 (that is, 100 values of y are "1" and the rest "0"), but the k_z is 800, or k_z=(8)k_y. To get around this, a random uniform variable between 0.999 and 1.001 (started from the same seed each time) is generated, spatially lagged using the same W as before, divided by the mean (in order to scale back to ~1.0), and elementwise-multiplied by z. Then the observations for which the random multiplier was slightly larger than 1.0 will pick up on the proper quantile ordering and result in k_z=k_y.
