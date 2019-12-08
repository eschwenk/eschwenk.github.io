---
name: 
tagline: Optimization code for the generation of feasible 3D grain boundary structures from atomic–resolution microscopy images.
abstract: Knowledge of atomistic structure is essential for understanding the properties of materials. While image simulation can facilitate a better understanding of the images in these instances, typical workflows for constructing atomistic models from images are manual and require expert discretion when identifying/interpreting individual features. The <code>ingrained</code> toolkit provides an automated way to generate an initial guess for the atomistic structure that balances geometrical constraints with a simulated "fit-to-image".
cover_path: "assets/images/ingrained_pano.png"
---

![image](https://drive.google.com/uc?export=view&id=1I28VP5ixBSQlLjvNSK9RcSi8u5TV9kG9){:width="71.5%"}

Grain boundary structure initialization from atomic-resolution HAADF STEM imaging
[[wiki](https://gitlab.com/eschwenk/ingrained/wikis/home)] [[paper](https://)]

## Getting started
This repo is currently private in order to protect the MaterialEyes&copy; source code while under development. 
Contact <a href="eschwenk@u.northwestern.edu">me</a> if you are are a potential user or developer and I will set you up with the proper permissions to view/contribute to the code.

## HAADF–STEM Image
This is an example of an atomic–resolution HAADF STEM image of a CdTe (100)-(110) grain boundary with tilt. In many optimization approaches, a solver might benefit from a good initial guess on where to start; That is what this code provides. It is assumed that the orientation and the chemical composition of each constituent crystal is known. 

<img src="https://drive.google.com/uc?export=view&id=1AVkLRqNtg9cmaJEs48yc7HJDIjooIvEQ" width="300">

image credit: <a href="https://aip.scitation.org/doi/10.1063/1.5123169">Guo, Jinglong, <it>et al.</it> Appl. Phys. Lett. <b>115</b>, 153901 (2019)</a>

## Example Usage
### <font color='#5EC6AA'>Input chemistry & orientation information for each grain.</font>
```python
# Import main ingrained package modules
from ingrained.structure import Bicrystal
from ingrained.optimize import CongruityBuilder

# For top grain
grain_1 = {}
grain_1["chemical_formula"] = "CdTe"
grain_1["space_group"] = "F-43m" 

# screen to viewer
grain_1["uvw_project"] = [1,1,0]  

# upward on screen
grain_1["uvw_upward"] = [-1,1,0] 

# CCW around "uvw_project"
grain_1["tilt_angle"] = 8

# maximum width (Å)
grain_1["max_dimension"] = 40     


# For bottom grain
grain_2 = {}
grain_2["chemical_formula"] = "CdTe"
grain_2["space_group"] = "F-43m"

# screen to viewer
grain_2["uvw_project"] = [1,1,0]

# upward on screen
grain_2["uvw_upward"] = [0,0,1]

# CCW around "uvw_project"
grain_2["tilt_angle"] = 0 

# maximum width (Å)
grain_2["max_dimension"] = 40
```
### <font color='#5EC6AA'> Initialize <code>Bicrystal</code> from grain information and setup optimization.</font> 
```python
bicrystal = Bicrystal([grain_1,grain_2], \
                       minmax_width=(10,40), \
                       minmax_depth=(8,20))

# Use pixel scaling (dm3) to constrain size
if dm3lib.DM3(dm3_path).pxsize[1].decode('UTF-8') == 'nm':
    pixsz = dm3lib.DM3(dm3_path).pxsize[0]*10
    
# Constraints 
iw = [-1,2.5]                # interface widths (Å)
df = [0.75,1.75]             # defocus values (Å)
px = [0.85*pixsz,1.15*pixsz] # allowable pixel sizes (Å)
bx = [0.0,0.15]              # x-border red. ratio (vert) 
by = [0.0,0.15]              # y-border red. ratio (horiz)

# Initialize ConguityBuilder to optimize fit 
congruity = CongruityBuilder(bicrystal=bicrystal,dm3=dm3_path)

# Define a starting point for optimization 
#    | iw | df | bx | by | px |
x0 = [-0.1, 1.3, 0, 0.1, pixsz]
```
### <font color='#5EC6AA'> Perform <b>constrained</b> minimization using a COBYLA solver.</font>
```python
res1 = congruity.find_correspondence(optimizer='COBYLA', \
       initial_solution=x0, constraint_list=[iw,df,bx,by,px])
⋮
Final Solution: 
>>> IW : -0.6078157487413525
>>> DF : 1.4319874724494737
>>> PX : 0.163556437666011
>>> BX : 0.1542803492239945
>>> BY : 0.11378092964375788
🌀 FOM : 0.7996850224295375 

Optimization terminated successfully.
         Current function value: 0.799685
         Iterations: 4
         Function evaluations: 805
```
## Example Output
The final parameterization provides an optimal correspondence solution between the experimental image and the proposed initial structure. Notice the high degree of similarity between the simulation and experiment!

<img src='https://drive.google.com/uc?export=view&id=1VIIjrKQ99OKIwCepBL4bjWnKo3MaAuoA' width="300">
