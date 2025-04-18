# fastfirespeed - Algorithm Explained
### Intro
We want to determine the maximum speed of a fire between two timesteps, t1 and t2. The fire perimeters are represented by multipolygons (sets of polygons). Let the perimeter at t1 be P1 and the perimeter at t2 be P2. One intuitive metric for the distance travelled by the fire is the maximum distance between P1 and P2. Simply, for each vertex in P2, we want to pair it with the nearest point in P1; then we want to find the longest of all these pairs. This concept is illustrated in the example below, taken from two timesteps of the East Troublesome Fire. The blue line shows the longest of these paths between the earlier timestep (darker) and the later timestep (lighter):

![image](figs/dist_example.png)

The naive way to compute this is to iterate over the outer polygon and then over the inner polygon, making O(n * m) comparisons for n points in P1 and m points in P2. This gets very expensive when working with big perimeters and many timesteps. We present a binning method to dramatically reduce the cost per step of computing these maximum spread vectors.

------------
Algorithm: BinnedFirespeed
    (inner_multipolygon, outer_multipolygon, matrix)
--------------------------
```
Iterate over outer_polygon in outer_multipolygon
    resample(outer_polygon)
    spot_check(outer_polygon, inner_multipolygon)
    if spot fire, compare with all inner polygons
    else, compare with only overlapping polygons
    Iterate over selected inner_polygon
        resample(inner_polygon)
        compute bounding_box for both polygons
        initialize bin_grid_inner over bounding_box
        initialize bin_grid_outer over bounding_box
        bin points in inner_polygon to bin_grid_inner
        add points in outer_polygon to bin_grid_outer
        compute inner_mask of occupied inner bins
        compute outer_mask of occupied outer bins
        initialize max_dist to -inf
        iterate over o_bin in outer_mask
            initialize ring = 0
            initialize unfound_flag = T
            while ring < max_ring
                compute list of occupied bins with l1-distance at most ring
                if list is not empty and unfound_flag is T
                        verify validity of combinations
                        set max_ring to ceil(sqrt(2)*ring) + slop
                        set unfound_flag = F
                if ring + 1 = max_ring and unfound_flag is F
                    save list of occupied bins to ib_list
                increment ring
            initialize temp_max_dist to -inf
            iterate over points p in o_bin
                initialize min_dist to inf
                iterate over inner_bin in ib_list
                    iterate over points q in inner_bin
                        compute dist(p, q)
                        save to min_dist if smaller
                save min_dist to temp_max_dist if greater
            save temp_max_dist to max_dist if greater
        save the minimum of these over all pairs including this outer polygon
    save the maximum over all outer polygons
return the distance and longest vector
```

------------
### Description

What is going on in the algorithm above? \
First, we need to iterate over all the polygons in the second timestep perimeter. We need to determine if they are spot fires (they do not overlap with the fire at the previous timestep). This will determine which polygons in the previous timestep we need to compare to. The algorithm finds the maximum distance over all desired pairs.

Second, we need to iterate over all the polygons in the first timestep perimeter. The algorithm finds the minimum distance computed over earlier timesteps with a fixed later timestep. Intuitively, the fire probably spread to place B from the nearest place A, but we want to find the longest of those A-B distances.

The meat of the algorithm: to compute longest distances efficiently over these combinations of polygons, we bin points on the perimeter, with a consistent grid between the first and second timestep. Again, we want to find the second-timestep point q with the maximum mininum distance to any first-timestep point p. 

Instead of iterating over these points directly we iterate over the bins occupied by second-timestep points. A nearby occupied first-timestep bin should therefore contain the nearest point, thus dramatically reducing the number of comparisons that need to be made between first- and second-timestep points.


