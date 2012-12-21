r.walk
======

A Grass exstension to analyse low-energy-cost way 


r.walk - Outputs a raster map layer showing the anisotropic cumulative cost of moving between different geographic locations on an input elevation raster map layer whose cell category values represent elevation combined with an input raster map layer whose cell values represent friction cost.
KEYWORDS

raster, cost surface, cumulative costs
SYNOPSIS

r.walk
r.walk help
r.walk [-knr] elevation=string friction=string output=string [start_points=string] [stop_points=string] [coordinate=x,y[,x,y,...]] [stop_coordinate=x,y[,x,y,...]] [max_cost=integer] [null_cost=float] [percent_memory=integer] [nseg=integer] [walk_coeff=a,b,c,d] [lambda=float] [slope_factor=float] [--overwrite] [--verbose] [--quiet]
Flags:

-k
Use the 'Knight's move'; slower, but more accurate
-n
Keep null values in output map
-r
Start with values in raster map
--overwrite
Allow output files to overwrite existing files
--verbose
Verbose module output
--quiet
Quiet module output
Parameters:

elevation=string
Name of elevation input raster map
friction=string
Name of input raster map containing friction costs
output=string
Name of raster map to contain results
start_points=string
Starting points vector map
stop_points=string
Stop points vector map
coordinate=x,y[,x,y,...]
The map E and N grid coordinates of a starting point (E,N)
stop_coordinate=x,y[,x,y,...]
The map E and N grid coordinates of a stopping point (E,N)
max_cost=integer
An optional maximum cumulative cost
Default: 0
null_cost=float
Cost assigned to null cells. By default, null cells are excluded
percent_memory=integer
Percent of map to keep in memory
Default: 100
nseg=integer
Number of the segment to create (segment library)
Default: 4
walk_coeff=a,b,c,d
Coefficients for walking energy formula parameters a,b,c,d
Default: 0.72,6.0,1.9998,-1.9998
lambda=float
Lambda coefficients for combining walking energy and friction cost
Default: 1.0
slope_factor=float
Slope factor determines travel energy cost per height step
Default: -0.2125
DESCRIPTION

r.walk outputs a raster map layer showing the lowest cumulative cost of moving between each cell and the user-specified starting points. It uses an input elevation raster map layer whose cell category values represent elevation, combined with a second input raster map layer whose cell values represent friction costs. This function is similar to r.cost, but in addiction to a friction map, it considers an anisotropic travel time due to the different walking speed associated with downhill and uphill movements.
The formula from Aitken 1977/Langmuir 1984 (based on Naismith's rule for walking times) has been used to estimate the cost parameters of specific slope intervals:

T= [(a)*(Delta S)] + [(b)*(Delta H uphill)] + [(c)*(Delta H moderate downhill)] + [(d)*(Delta H steep downhill)]

where:
T is time of movement in seconds,
Delta S is the distance covered in meters,
Delta H is the altitude difference in meter.

The a, b, c, d parameters take in account movement speed in the different conditions and are linked to:

a: underfoot condition (a=1/walking_speed)
b: underfoot condition and cost associated to movement uphill
c: underfoot condition and cost associated to movement moderate downhill
d: underfoot condition and cost associated to movement steep downhill
It has been proved that moving downhill is favourable up to a specific slope value threshold, after that it becomes unfavourable. The default slope value threshold (slope factor) is -0.2125, corresponding to tan(-12), calibrated on human behaviour (>5 and <12 degrees: moderate downhill; >12 degrees: steep downhill). The default values for a, b, c, d are those proposed by Langmuir (0.72, 6.0, 1.9998, -1.9998), based on man walking effort in standard conditions.
The lambda parameter of the linear equation combining movement and friction costs:
total cost = movement time cost + (lambda) * friction costs
must be set in the option section of r.walk.

For a more accurate result, the "knight's move" option can be used (although it is more time consuming). In the diagram below, the center location (O) represents a grid cell from which cumulative distances are calculated. Those neighbours marked with an x are always considered for cumulative cost updates. With the "knight's move" option, the neighbours marked with a K are also considered.

  K   K 
K x x x K
  x O x
K x x x K
  K   K
The minimum cumulative costs are computed using Dijkstra's algorithm, that find an optimum solution (for more details see r.cost, that uses the same algorithm).

Once r.walk computes the cumulative cost map as a linear combination of friction cost (from friction map) and the altitude and distance covered (from the digital elevation model), r.drain can be used to find the minimum cost path.

SEE ALSO

r.cost, r.drain, r.in.ascii, r.mapcalc, r.out.ascii
REFERENCES

Aitken, R. 1977. Wilderness areas in Scotland. Unpublished Ph.D. thesis. University of Aberdeen.
Steno Fontanari, University of Trento, Italy, Ingegneria per l'Ambiente e il Territorio, 2000-2001. Svilluppo di metodologie GIS per la determinazione dell'accessibilità territoriale come supporto alle decisioni nella gestione ambientale.
Langmuir, E. 1984. Mountaincraft and leadership. The Scottish Sports Council/MLTB. Cordee, Leicester.
