# Element Objects
written by Spencer Wright
on 2016.04.21

## Purpose
The purpose of this document is to explain the core properties of the different object types that are used in nTopology Element. These objects are viewed here from the vantage point of a user, and this document is not a technical specification. Some aspects of this document will change over time as user needs (and Element's capabilities) evolve, but in general I've tried to represent both the current state of Element Objects and any anticipated extensions to them.

## Objects overview
Element can generate and operate on six different types of Object:
* Project - the collection of all objects used in the design process
* Volume - a region of 3D space, essentially a shape, blob, etc
* Surface - a 2-dimensional object in 3D space.  A surface is closed if it encloses volume (divides space into inside and outside regions)
* Lattice - a structure composed of points in space and line segments connecting them.
* Rule - a method of creating lattices.
* Modifier - a tool for acting on lattice, whose effect varies by location in space.

Projects are Element's natural working environment. Volumes are generally interpreted as solid bodies.
Lattices are 2D or 3D structures based (at least loosely) on a system of repeating beams, nodes, or volumes.
Rules define some system for generating lattices in 3D or 2D space.
Modifiers are used to change the properties of lattices and volumes.

This document isn't meant to describe in detail the workflow through Element, but I should note that we make a clear distinction between defining the topology of a lattice (which is represented today as a series of one dimensional points and line segments in 3D space) and thickening that lattice (which today is done by generating a 3D mesh that surrounds the lattice beams). In other words: First you create an unthickened lattice, then you thicken it.

### Projects
Projects are the topmost thing that Element deals with. When the user saves an `.elem` file, the entire project (and nothing else) is saved. Projects may contain any combination of Volumes, Surfaces, Rules, Lattices and Modifiers. It also seems conceivable that Projects might be able to (recursively) contain other Projects; this is probably a topic for further conversation.

### Volumes
The exact definition of a volume will change depending on the level of detail that you approach an object from; things that look solid from close up might look more like lattices as you zoom out. Generally, though, volumes have high mass relative to their bounding boxes - and in the context of Element's workflow, volumes are anything that you might use to define the bounding envelope for a lattice.

Element currently represents volumes by mesh bodies importable through STL or OBJ formats.
In the long run, it's highly likely that we'll allow volumes to be represented with boundary representation geometry - and at least somewhat likely that we'd want to allow for voxel representations as well.

Because our core users are typically designing physical objects that will be manufactured and put into service in larger assemblies, volumes will very frequently have some surfaces which represent important mechanical features. It's likely that our users will have defined properties of those mechanical features in their solid CAD packages, and ideally they would be easily importable into Element. Alternately, we'll probably want to offer some kind of feature recognition and definition into our software.

Volumes have their own coordinate system and (possibly implicit, as in the case of STL) system of measurement; it would be helpful if Element allowed users to translate, rotate, and scale those properties within the Element Project's coordinate space. That has not been implemented to date, however. Currently, when a user imports a volume its coordinate system is locked and coincident with the project coordinate system.

### Surfaces
Surfaces define geometry in two dimensions. Currently, nTopology Element supports mesh surfaces; it stands to reason that we would want to add other CAD surfaces in the future. For many users, Surfaces are simply a way to represent the Volumes that they enclose; others will use Surfaces to generate lattices using Face or Beam rules.

Because of the fact that Element only generates surface Lattices based on the input mesh topology, Surface objects aren't really treated as a distinct thing currently. Instead, the user can interact with meshes in two different ways - as either a Surface or a Volume. This is generally fine, but it's also useful (as a user) to distinguish between whether you're treating the input geometry as one or the other - and within nTopology Element Free, the two require different workflows and approaches.

### Rules
Rules define the topology of a lattice. Rules consist of two properties: Tessellation and Unit. These terms aren't referenced explicitly in Element Free, but the user can view them in the rule preview window of the Generate Lattice tool. There, Tessellations are shown with black and grey lines, and Units are shown in red lines.

I should note that the concept of a "rule set" (which existed in Element Pro Beta but is not included in Element Free) doesn't seem particularly useful to me at this point, though that probably warrants further discussion.

#### Tessellation
Tessellation describes the method by which the Unit (which is abstract and not part of the design per se) is instantiated in the design space. Conceptually, Tessellations fall into three categories, though Element Free doesn't explicitly distinguish between them.
##### Volumetric
Volumetric Tessellations are generated directly in 3D space, and are based on 3D geometries. For instance, a (regular) hex prism tessellation fills space by nesting identical hex prisms next to and on top of each other. There are also semiregular Volumetric Tessellations (such as Oct-Tet) and irregular Volumetric Tessellations (such as Voronoi and A15). Element Pro Beta contained all three of these; Element Free contains only regular and semiregular.
##### Face
Face Tessellations arrange units based on 2D (Euclidean or non Euclidean) geometries. They look at the surface of a part and create lattice structures based on that surface's topology. Face Tessellations were used extensively in nTopology's early textile work. Some of them look only at a single mesh face at a time, and others might look at patterns of faces. It should be noted that today, Face Tessellations operate directly on the faces of a mesh (STL/OBJ). It is our intention to extend Face Tessellations to BREP inputs, though it's likely that that workflow will involve an intermediate step that's similar to a mesh.
##### Beam
Beam Tessellations arrange units based on 1D structures - line segments located in 3D space. nTopology Element Free's "surface lines" rule is the simplest version of a Beam Tessellation, as it creates a lattice beam coincident with every edge of the input mesh. 

#### Units
Units describe what lattice topology is propagated throughout the Tessellation. Today, nTopology Element Free supports ONLY line Units. It's likely that in the future we would support Spline Units, and possibly surface and volume units as well.

Regardless: A Unit maps some features (lines for now and possibly splines etc in the future) to the Tessellation. 

### Lattices
Currently, lattices are made up of two things: nodes and beams. Nodes have locations in XYZ space, and beams connect two nodes.

It is our plan to extend nodes to have an additional property: `diameter`. Beams will also be extended to have a `transition` property, which will control how their thicknesses vary across their length. To start, I think we should only support one `transition` value - `linear`. Hence, beams whose nodes have different `diameters` will take the shape of a conical frustum. At some point in the future it might make sense to add other properties - I'm thinking in particular of alternate transitions, some of which might take additional diameter values.

It's also conceivable that we'd want to allow for lattices that are based on the aforementioned (potential) spline, surface, and volume rules. To the user these would seem similar (identical?) to standard beam lattices, though it seems possible that they would be operated on by a different set of functions.

### Modifiers
Modifiers are functions that act on lattices; the strength of this action depends on the lattice elements location in space.
Point-based modifiers act according to a locations distance from those points, controlled by falloff parameters.

Currently, modifiers can contain only points. Points have XYZ locations, ranges, and falloff curves. Point modifiers can currently be used to control beam thickness of a lattice, but as we showed in Pro Beta they can also be used to control lattice topology. I will note, however, that the way that we dealt with modifiers & topology was a bit limited. It makes sense to allow, for instance, volume lattices to be generated within a volume but only within some range of a point modifier. Ditto surface lattices; there's no reason why we can't generate surface lattices only within a certain area. I think we should make these extensions in the near future.

In addition to point modifiers, Pro Beta used FEA to generate scalar field modifiers. These act in a similar way as point modifiers do, but they operate in a shaped (voxel) region rather than radiating from a single point. Again, it makes sense that scalar field modifiers should be able to control variable thickness, topology, and whether or not a lattice is generated in the first place.

I believe that we should also consider modifiers that aid in design for manufacturing. These would consist of an orientation and a number of rules that improve printability. It also stands to reason that we would treat any input from FEA as a modifier, though I'm not sure that they should operate in the same ways that our earlier implementation did.
