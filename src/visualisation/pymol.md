# Pymol API tutorial

Pymol can be imported and called from python code. This allows scripting and reproducability, without having to mess around with clunky GUIs that belong in the 90s. 



## Installation
Information given here: https://pymol.org/2/  
Conda installation is available.  
License free for academia, available here: https://pymol.org/edu/?q=educational/  They will email you a download link. Open up the GUI and it will request the license file, just give it that .lic you downloaded.


## Basic use

First we need to import pymol. If this doesn't work, check your installation.  
`import pymol`


Now we need to load Pymol, easiest way is to load a prepared geometry file. Pymol only accepts certain file types, I usually use XYZ  

`​pymol.finish_launching(['pymol', '-qc'])`  
​
`pymol.cmd.load('geometry.xyz')`


### Visualizing atoms
Here are some basic visualizaton options, to show the atoms we are looking at:

Show atoms as spheres:   
`pymol.cmd.show('spheres')`  

Use a cartoon representation:  
`pymol.cmd.show('cartoon')`  

Set sizes for different elements:  
`pymol.cmd.set('sphere_scale', 0.27,'(elem N, elem O)')`  
`pymol.cmd.set('sphere_scale', 0.9,'(elem Au)')`  

Show sticks for bonds:  
`pymol.cmd.show('sticks')`  

Give those sticks a specific radius:  
`pymol.cmd.set('stick_radius',0.15)`  

Set specific colours for different elements:  
`pymol.cmd.color('0x6495ED', '(elem N)')`  
`pymol.cmd.color('0xF08080', '(elem O)')`  

Set named preset colour for specific element:  
`pymol.cmd.color('gold', '(elem Au)')`  


### Rotation
Easy:  
`pymol.cmd.turn('x',70)`  
`pymol.cmd.turn('z',180)`  

### Preparing lighting
Many options:  
```python
pymol.cmd.set('light_count',8)
pymol.cmd.set('spec_count',1)  
pymol.cmd.set('shininess', 1)
pymol.cmd.set('specular', 0.55)  
pymol.cmd.set('ambient',0.3)  
pymol.cmd.set('direct',0)  
pymol.cmd.set('reflect',1.)  
pymol.cmd.set('antialias',2.)  
pymol.cmd.set('ray_shadow_decay_factor', 0.1)
pymol.cmd.set('ray_shadow_decay_range', 2)
pymol.cmd.set('ray_trace_mode',3)
pymol.cmd.set('sphere_mode',5)
```


### Rendering image

`pymol.cmd.ray(1200,800)`  

`basename = 'fig2'`  
`pymol.cmd.png('./'+basename + '.png',width=1200,height=800,dpi=400)`  
`print('file written')`  

### Quit
`pymol.cmd.quit()`


## Example script
[example_script.py](https://github.com/maurergroup/grouptools/blob/master/pymol_scripts/render.py)

## Pymol wiki (for commands etc.)

https://pymolwiki.org/index.php/Main_Page
