# pyfirespread - algorithm explained
### use case
We want to determine the maximum speed of a fire between two timesteps. 

The earlier timestep perimeter is represented by a polygon P1 and the later timestep perimeter is represented by a polygon P2.

As a proxy for the speed of the fire we want to compute the maximum distance over the set of minumum distances between pairs of points p_i, q_j with p_i in P1 and q_j in P2.