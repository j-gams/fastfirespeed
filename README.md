# pyfirespread - Algorithm Explained
### Intro
We want to determine the maximum speed of a fire between two timesteps, t1 and t2. The fire perimeters are represented by multipolygons (sets of polygons). Let the perimeter at t1 be P1 and the perimeter at t2 be P2. One intuitive metric for the distance travelled by the fire is the maximum distance between P1 and P2. Mathematically, for vertices defining the polygon $p_i \in P1$, $q_j \in P2$


As a proxy for the speed of the fire we want to compute the maximum distance over the set of minumum distances between pairs of points p_i, q_j with p_i in P1 and q_j in P2.