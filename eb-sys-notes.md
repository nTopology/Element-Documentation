# System architecture

## Short to medium term goals

### Separate UI user interaction from task invocation and responses

Front end: For each tool, separate into two pieces:
  1. one part that is directly connected to the UI
    * just displays and collects user input for specific parameters
    * within given types, ranges, constraints
  2. another part that uses those parameters
    * to assemble and issue commands to the back end

### Unify and encapsulate major objects
We have discussed having a more centralized data management method.
There were questions about issues with centralizing everything.
I would propose that we start by centralizing the management of
the main types of objects that we use.

The 4 main entities that we use.
I would suggest that we "black-box" these - create APIs / wrappers
around them; and have data managers for each class of entity (not of each of the implmenting classes)

These are abstract entities not data stuctures or implementations,
so I use a different name to be clear about that.
  1. GenGraph:
     * A graph (nodes and edges (edges not directed and have two distinct nodes - do we ever allow loop edges?))
     * This graph is embedded in R^3
     * Nodes and edges may have attributes associated with them
     * Operations that must be supported:
       -  Add and remove nodes and edges
       -  Set and get attributes from nodes and edges (at least node position and radius)
  2. Surface:
     * A 2-D manifold or manifold-with-boundary embedded in R^3
     * Operations that must be supported:
  3. Volume:
     * A subset of R^3
     * Operations that must be supported:
       - Test if a point is inside, on the boundary, or outside
       - Find the intersection(s) of an line segment, ray, or line with the volume
       - Boolean operations
  4. Func3D:
     * Operations that must be supported:
       - Given a 3D point, return a value   

Implementations
  1. GenGraph
     * LatticeGraph
  2. Surface
     * Polymesh (may be closed or not)
     * Implicit from Func3D (level set)
  3. Volume
     * Closed polymesh boundary
     * Implicit from Func3D (level set gives boundary)
     * Implicit from Func3D (boolean on values specify in/out)
  4. Func3D
     * Programmed ad hoc (e.g., typical current attractor) 
     * Scalars on 3D grid (voxels) and interpolate

### Organize logging, journaling

### Organize error handling

### Organize File I/O

