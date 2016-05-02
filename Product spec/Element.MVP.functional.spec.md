#nTopology Element MVP
##Functional Specification

Spencer Wright
Last Updated: 2016.02.29

#Overview
nTopology Element is a desktop app that can be used to design complex 3D lattice structures. This specification describes Element's minimum viable functionality.

Like our software, **this spec is a work in progress.** If anything needs clarification, please refer to the [MVP Trello board](https://trello.com/b/nWJ1KVVW), or the wireframes that, as of 2016.03.10, are mostly complete and housed in the same folder as this document.

**This is not a technical specification;** it simply describes what the user sees when they interact with Element.

#To do
This document is incomplete in the following ways:
* Naming conventions - what should new objects be named by default?
* Double clicking on objects - does it do anything?
* Icons - I need to find some from The Noun Project still

#Nongoals
The MVP of Element does *not* include the following features:
* Imported attractor data
* Field attractor method
* Unicorn thickening
* Any rule builder or rule editor
* Any DFM (overhang) analysis
* Disruptive lattice generation
* Any mechanical simulation (FEA)
* Any morph, warp, or subdivide functionality

#Scenarios
##Scenario 1: Franco
Franco is a designer working in the fashion world. He works in a variety of design tools, and uses both (pretty hacky - he's more designer than programmer) algorithms and manual control (i.e. "fuckin around with some lines") to create aesthetically pleasing (or whatever) structures.

##Scenario 2: Harvey
Harvey is an old hand engineer concerned with manufacturability and function. His software of choice is NX, and in general he kinda looks down on meshes... but what he really cares about is designing better parts, and if he can do that with meshes, then he'll figure out how to make that work. 

#Element Flowchart
Gah! It's hard to put flowcharts in markdown files. Head here instead: https://www.lucidchart.com/invitations/accept/eed46dac-1fe4-4403-8629-b4f79381a9e9

#Global UI settings
* Any object that's colored red should use RGB 1.0 , 0.0 , 0.0
* Any object that's colored orange should use RGB 1.0 , 0.6 , 0.0

#Window by window spec
Element makes copious (probably too much) use of modal windows. As much as possible, we should reuse UI elements, though it's worth noting that in the past some UIs have been reused and *not* updated to reflect changes in functionality. Below, see descriptions of what each of Element's windows does.

##Splash screen
Yes! We can haz splash screen. 

The splash screen actually fills an important function: it primes the user to make lattices through our (kinda weird) workflow. The splash screen is a small window (600px*400px) that contains our hexagon logo and logotype in blue. On the bottom of the window, we help users understand our workflow by displaying the following:
    'Generate >> Edit >> Thicken'

The splash screen should display for two seconds, then disappear. Then the main window should open.

##Main window
The main window doesn't *do* very much itself; it's mostly a portal to other functional areas of the program. The main window has a few big features:
* **Top bar.** This has our hexagon logo/favicon on the top left corner, and says "Element" just to the right of that. When the user has a saved project open, the top bar says "filename.elm - Element".
* **Menus.** This has two elements in it:
    - **File menu.** This contains things that relate to files. Namely:
        + Import mesh. This allows the user to import an STL or OBJ file as a new object in the current project.
        + Open project. This allows the user to open an existing nTopology Element project.
        + Save project. This allows the user to save the current project as an .elm file.
        + Save project as. This allows the user to save a new copy of the current project, leaving the old version intact.
        + Export meshes. When the user clicks this, an export dialog opens that allows the user to select which mesh(es) she wants to export.
        + Exit. This allows the user to exit Element.
    - **Help menu.** This contains **other stuff.* Namely:
        + About. This displays a popup menu that says the Element version number and has copyright info.
        + Help. This is just a hyperlink - for now it just opens up http://ntopology.com

* **Action buttons.** This is to the right of the menus, centered on the scene, and has four items:
    - **Generate Lattice.** Opens the Generate Lattice window.
    - **Edit Lattice.** Opens the Edit Lattice window.
    - **Modifiers.** Opens the Make/Edit Modifiers window.
    - **Thicken Lattice.** Opens the Thicken Lattice window.

* **Scene.** This is the main viewport; it shows all visible objects in 3D space.
* **Object browser.** This shows an ordered list of all of the objects that are in the current project. The list is chronological; objects that were created/imported first are on top. A few subfeatures of the object browser:
    - **Visibility toggle.** Next to each item is a light bulb. When the light bulb is clicked, visibility on the object is toggled - and the light bulb itself toggles on/off.
    - **Center view.** Next to each item is an icon of a crosshair. When the user clicks on the crosshair, the Scene centers on that object.
    - **Info button.** Next to each item is an icon of the letter "i". When the user clicks on the "i", an info window pops up. The info window is 600px*400px and shows information like object name, object type, and various object properties (which depend on object type). The attribute view opens up next to the selected object & has a comic book connection to the object. ping @abhee if you have any questions about the look & feel of this.
    - **Object Icon.** To the left of each item is an icon representing the object type. We will have 3 icons in the MVP, one for Lattices, one for Meshes, & one for attractors. Since it is not alway's clear what each object type is, especially as now you have to click on the 'i' to see the attribute view, the icon is a fast and easy way for people to know what each objecct is.
* **Tool tips.** When the user hovers their cursor over any of the objects above for three seconds, a tool tip appears just to the right of the cursor. Tool tips should be short, descriptive, and give the user any information that isn't immediately obvious. For instance, the tool tip for the visibility toggle could be something like "Toggles visibility of the object." Spencer will be responsible for writing these tooltips, but obviously anyone is welcome to help. There's a database of tooltips [here](https://docs.google.com/a/studiobrad.co/spreadsheets/d/1ICiF5hx_KqPf-bfU4claklbGRcC1gV6Qt0M-gZ9kZR4/edit?usp=sharing).

##Importing a mesh
Importing a mesh is a really important part of most people's workflows, but we treat it as if it's NBD. Just click File->Import Mesh and a standard dialog box pops up and lets you select an STL/OBJ file. When you click "Open," a dialog box pops up that says "Importing..." and when that's done the new object shows up in the Object browser. The object should have the same name as the mesh's original filename.

##Generate Lattice window
Generating lattices! So important. This is a big part of our software.

When the user opens the generate lattice window, it pops up on top of the main window. By default its size should be 1200px*800px, with the caveat that if the user's desktop scale is set at something bigger than 1x, the window should be effectively smaller so that it doesn't extend off of the viewable screen.

Note that the user does **not** need to have a mesh selected in order to open the Generate Lattice window.

On the left side of the Generate Lattice window is an input bar. On the right side is the scene, which is the same in every way as the main window scene.

At the top of the input bar is a text input window that lets the user set the new lattice's name (default is "New lattice name"). Below that is a dropdown that lets the user select a mesh to generate the lattice in/on. The user doesn't need to select a mesh; if they don't, the default "No mesh selected" is used. Below that is a dropdown that lets the user select a rule. If no mesh is selected, then only volume rules are displayed. If a mesh is selected, then volume rules and "surface edges" are displayed.

At the bottom of the input bar is a cancel button. If the user presses cancel the window is closed with no change to the project.

If the user selects a mesh, the mesh is shown in the scene. If the user selects a volume rule, a rule preview window displays below the rule dropdown and a number of controls display below that. The preview window is annotated with dimensions so that the user knows how to set scale. These will vary depending on unit type; a cube cell will have three dimensions (which are essentially XYZ space) but a hex prism will only have two (the length of a side, and the height).

If the user doesn't select a mesh, but selects the "surface edges" rule, the generate lattice button is disabled.

If the user is making a volume lattice with no mesh, "generate" makes one cell in the middle of the scene. The user can then press "step," and the lattice will grow by one cell in each direction.

If the user is using a mesh to make a volume lattice, "filling accuracy" (which does **not** appear in the input dialog, but which we use on the backend regardless) is set at half of the smallest dimension of a cell. Pressing "set start position" places the first cell's centroid at that location; pressing "generate" fills the mesh, "step" grows the lattice by one cell in all directions, "warp to fit" warps the lattice to fit the mesh, and "trim" trims the lattice so that it doesn't extend beyond the bounds of the mesh.

Once the user has pressed "generate" (regardless of lattice type), a "save and close" button appears on the bottom left of the window. If the user presses "save and close," the lattice is created as an object and visibility is set to ON.

The user can, at any point in the process, toggle visibility of the lattice or the mesh. There are also tooltips for every button in the entire window.

#Edit Lattice window
The Edit Lattice window allows the user to make a range of manual and semi-automated changes. It contains some of the features from the old "Lattice Utilities," and one additional feature that was previously housed in the main view.

When the user opens the Edit Lattice window, it pops up on top of the main window. By default its size should be 1200px*800px, with the caveat that if the user's desktop scale is set at something bigger than 1x, the window should be effectively smaller so that it doesn't extend off of the viewable screen.

Note that the user does **not** need to have a mesh selected in order to open the Edit Lattice window.

On the left side of the Edit Lattice window is an input bar. On the right side is the scene, which is the same in every way as the main window scene.

When the Edit Lattice window opens, all that appears in the input bar is an "Select a lattice..." dropdown and a cancel button at the bottom. 

Once the user has selected a lattice, the editing options appear below in tabbed format:
* **Move mode.** This mode allows the user to manually (by clicking and using the gimbal) or automatically (by using a second lattice as an attractor) move nodes and beams.
* **Merge & split.** This allows the user to merge two lattices into one, or split a lattice into contiguous parts.
* **Trim.** This mode allows the user to select an arbitrary mesh and then trim the selected lattice to that mesh.
* **Clean up.** This mode allows the user to manually delete beams or automatically remove any open beams.

When the user selects one of these tabs, a text area appears below that explains the editing mode. Some input fields also appear (depending on the tool). 

A few notes on saving, etc:
* The "cancel" button remains throughout all of the tabbed views; if the user clicks it, the window closes with no change to the scene objects.
* The "overwrite," "save as," and "save as new" buttons should be greyed out (disabled) if the user has not made any edits to the lattice.
* If the user makes edits in one tab and then changes tabs without clicking "overwrite," "save as," or "save as new," then those changes are reset.
* If the user makes changes and clicks "overwrite," the edited lattice is overwritten with the changes, and the window is closed.
* If the user makes changes and clicks "save as," then a small window (400px*150px) pops up and asks the user to input a new object name. The user can then click "save as" again, and a new object is created (the old object remains unchanged from its original state) and the edit lattice window closes.
* The "merge & split" window has slightly different save options; those are described below.

##Move mode
Move mode has two editing methods:

###Manual move
Here the user simply clicks on elements (beams/nodes) in the scene. When an item (or many items) are selected, a gimbal appears that allows the user to move them in three dimensional space.

###Automatic move
Here the user selects an attractor lattice and sets the snap distance and maximum distance. Once an attractor lattice is set, the view updates to show all nodes which will be moved (notated by displaying a blue dot at each node to be moved). If the user changes the value in one of the input fields and then changes focus, the view updates. The user can make however many changes they wish, and the view will continue to update. When the user clicks "Move selected nodes", the moved nodes snap to their new locations.

##Merge & split mode
Merge & split mode has two editing methods:

###Split
If the user clicks "split," the active lattice is separated into its contiguous parts. This is analagous to Magics' "Parts to shells" feature. If the user makes a "split" change and then clicks "save as new," the original lattice is unchanged and new lattice objects are created, with the naming structure "oldLatticeName\_1", "oldLatticeName\_2", etc.

###Merge
In merge mode, the user select a second lattice from a dropdown menu; by default it's colored blue. The user can then click "merge," and the second lattice changes to red. If the user then clicks "save as new," then a small dialog window opens and prompts the user to input a new object name. The user can then click "save as" in that small window, and a new object is created. Note that merging does NOT require nodal connectivity between the two lattices. Note also that this "save as" window should look the same as the one described above.

##Trim mode
In trim mode, the user can select an arbitrary mesh from the project and then trim the active lattice(s) to that mesh. The user selects the mesh from a dropdown menu and then clicks "trim lattice(s)". 

##Clean up mode
Clean up mode has two functions: manual and removing open edges.

###Manual delete
Here the user can click on beams in the scene and press the `delete` key to delete them.

###Remove open beams
"Open beams" are defined as any beam which is connected on fewer than two sides. This includes "fingers" (beams that are connected at only one end) and "sticks" (beams that are free floating and not connected on either end). When the user clicks "remove open beams", every open beam is removed. Note that "remove open beams" only operates once - there may be open beams left over at the end of the process.

#Modifiers window
First:
* Let's define a **modifier** as something that can be used to control variable lattice properties.
* Let's define an **attractor** as a point in space which has a position and a falloff profile and which can be used to control variable lattice properties.

Currently, a modifier is a collection of one or more attractors. At some point in the future we may add other modifier types, but that's all we're supporting right now.

The modifier window allows the user to create new modifiers, edit existing modifiers, and preview the effect that modifiers will have on lattices or meshes.

When the user opens the Modifier window, it pops up on top of the main window. By default its size should be 1200px*800px, with the caveat that if the user's desktop scale is set at something bigger than 1x, the window should be effectively smaller so that it doesn't extend off of the viewable screen.

Note that the user does **not** need to have a mesh selected in order to open the Modifier window.

On the left side of the Modifier window is an input bar. On the right side is the scene, which is the same in every way as the main window scene.

The Modifier window's input bar has the following features:

* Create new modifier. At the top of the input bar there is a text input box that allows the user to type a new name and click "Create." If the user does that, the new modifier appears below in the "Modifiers" list.
* Display an object. The user can select an object (lattice or mesh) to display, and toggle that object's visibility. The object is displayed with heatmap by default; the heatmap cannot be turned off. The heatmap is driven by whatever modifier is selected; if no modifier is selected, then the heatmap is pure blue.
* Modifier list. Shows all of the modifiers in the current project, and allows the visibility of each to be toggled. Also allows the user to select a modifier, which makes its attractors display in the Attractor list below.
* Attractor list. Shows all of the attractors in the selected modifier.
* Add attractor button. If the user clicks this, the program enters "add attractor" mode. If the user then clicks in the scene, an attractor is created at that position. If the user then clicks anywhere else in the window, or presses `esc`, the window goes back into the default mode.
* Attractor position. Displays the x,y,z position of the currently selected attractor. Also allows for the user to change those values.
* Falloff curve. Displays the falloff of the currently selected attractor. Options are:
    - Linear
    - Cosine
* Falloff range. This is displayed right below the bottom right corner of the falloff curve, to show that it sets the value for the overall falloff.
* Save all. Saves all of the modifiers and closes the window.
* Cancel. Clears all pending changes and closes the window.

Most of the functionality of the Modifier window is described in the wireframes, but a few notes here:
* Lattice heat maps should look like mesh heat maps. If a beam is unaffected by the current modifier, it's blue; if it's affected by the modifier then it'll get redder and redder.
* Attractors can be moved around the scene via the gimbal. The user can select attractors by clicking on them in the scene or in the attractor list.
* When an attractor is selected, it displays as orange (RGB 1.0 , 0.6 , 0.0 -- HEX #FF9900). When an attractor is not selected, it displays as red (RGB 1.0 , 0.0 , 0.0 -- HEX #FF0000).

#Thicken lattice window
Can you believe we got this far? I can't.

The Thicken Lattice window does one thing: thicken lattices. When the user opens the Thicken Lattice window, it pops up on top of the main window. By default its size should be 1200px*800px, with the caveat that if the user's desktop scale is set at something bigger than 1x, the window should be effectively smaller so that it doesn't extend off of the viewable screen.

Note that the user does **not** need to have a mesh selected in order to open the Thicken Lattice window.

On the left side of the Thicken Lattice window is an input bar. On the right side is the scene, which is the same in every way as the main window scene.

When the Thicken Lattice window opens, there's just a "Select a lattice..." dropdown menu and a uniform/variable radio button. When the user selects a lattice, it displays in the scene. When the user selects uniform/variable, that mode's input fields are displayed below.

With uniform thickening, the only options are Diameter, Resolution, and Node smoothing. Once the user has input values for each of these, he can click "Generate." Doing so will cause a progress bar to appear. Once the lattice has been thickened, the user can either edit his settings and click "generate" again, or click "refine & smooth" (which is greyed out when nothing is thickened).

With variable thickening, the user can select a modifier and input values for minimum and maximum diameter. Note that the modifier's options do **not** appear in the thicken lattice tool anywhere; if you want to change a modifier's settings, then you need to go into the Modifier window. Again, the user can play with the settings and click "generate," then optionally click "refine & smooth". 

Once a thickened lattice has been generated, the user can click "save" to save it as a new object and close the window. The user can also click "cancel" at any point to discard changes and close the window.