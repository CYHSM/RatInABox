# RatInABox 🐀📦

RatInABox is a toolkit for simulating navigation and/or hippocampal-entorhinal cell types. Essentially it simulates a rat 🐀, with a brain🧠, in a box 📦. With it you can

* Generate pseudo-realistic trajectories for rats foraging/exploring 1 and 2D environments
* Simulate spatially selective cells typically found in the Hippocampal-Entorhinal system (place cells, grid cells, boundary vector cells, and velocity cells). 

~~Gridworld~~ RatInABox represents a clean departure from pre-discretised "gridworld" models which have typically been used for simulating hippocampal navigation. Position and neuronal firing rates are calculated online with float precision. 

`RatInABox` contains just three classes: 

1. `Environment()`: The environment/maze that the agent lives in. 1- or 2-dimensional. Handles geometry, walls, boundary conditions etc.
2. `Agent()`: The agent (or "rat") moving around the Environment. 
3. `Neurons()`: A population of neurons. Neurons have firing rates determined by the state of the Agent (e.g. its position). 

Here's an animation of the kind of simulation you can run. It shows an agent randomly exploring a 2D environment with a wall. Four populations of cells (place cells, grid cells, boundary vector cells and velocity cells) "fire" as the agent explores. Below shows the code needed to replicate this exact simulation (13 lines).

![](./readme_figs/whole_simulation.gif)

## Key features

* Flexible: Generate arbitrarily complex environments 
* Realistic: Simulate large realistic populations of neuronal cell types known to be found in the brain. 
* Fast: Simulating 1 hour of exploration in a 2D environment with 100 place cells (dt=10 ms) take 20 secs. 1 minutes exploration takes 3 seconds. Cells are rate based or Poisson spiking. 
* Precise: No more pre-discretised positions, tabular state spaces, or jerky movement policies. It's all continuous here. 
* Plotting: Easy plot animate trajectories, firing rate timeseries', spike rasters, receptive fields, heat maps and more using our plotting functions. 
* Easy: sensible default parameters mean you can have realisitic simulation data to work with in <20 lines of code. 

## Requirements
* Python 3.7+
* NumPy
* Scipy
* Matplotlib
* Jupyter (optional)

## Installation 

## Feature run-down
Here is a list of features loosely organised into three categories: those pertaining to (i) the Environment, (ii) the Agent and (iii) the Neurons. 

### (i) `Environment()` features
#### Walls 
Arbitrarily add walls to the environment to replicate any desired maze structure. The following code shows how to add walls to make a multicompartment environment like used in Carpenter et al. (2015). 
```
Env.add_wall([[0.3,0.0],[0.3,0.5]])
```
![](./figures/readme/walls.pdf)

#### Boundary conditions 
Boundary conditions can be "periodic" or "solid". Place cells and the Agent will respect boundaries accordingly.
![](./figures/readme/boundary_conditions.pdf)

#### 1- or 2-dimensions 
Most features work in both 1 and 2 dimensions. Some don't (e.g. walls, boundary_vector_cells aren't defined in 1D)
![](./figures/readme/one_dimension.pdf)


### (ii) `Agent()` features
#### Wall repelling 
Walls mildly repel the agents. Coupled with the finite turning speed this creates a combined effect that the agent is encourged over explore walls and corners (as shown in ...et al.). This can of course be turned off.
```
Αg.walls_repel = True #False
```
![](./figures/readme/walls_repel.pdf)

#### Random motion model
Motion is modelled stochastically. In 2D speed and rotational speed take constrained random walks governed by Ornstein-Uhlenbeck processes. You can change the variance nad coherence times of these processes to control the shape of the trajectory.
```
Agent.speed_mean = 0.2
Agent.speed_std = 0.1
Agent.speed_coherence_time = 3

Agent.rotation_velocity_std = 0.1
Agent.rotational_velocity_coherence_time = 3
```
![](./figures/readme/walls_repel.pdf)


#### Policy control 
By default the movement policy is an uncontrolled "smooth" random walk (displayed above) where the velocity is governed by an Ornstein-Uhlenbeck process. It is possible, however, to manually pass a "drift velocity" to the Agent, towards which its velocity will drift. We envisage this being use, for example, by an Actor-Critic system to control the Agent. The actor-critic system could take as inout (conveniently) the firing rate of some Neurons(). For demonstration here we set a radial drift velocity to encourage circular motion.   
```
Agent.update(drift_velocity=drift_velocity)
```
![](./figures/readme/place_cell_geometry.pdf)


### (iii) `Neuron()` features 
#### Geometry
Choose how you want place cells to interact with walls in the environment. We provide three types of geometries. 
![](./figures/readme/place_cell_geometry.pdf)

#### Spiking 
All neurons are rate based. Additionally we generate spikes as though they were Poisson neuros. These are stored in `Neurons.history['spikes']`. The max and min firing rates can be set with `Neurons.max_fr` and  `Neurons.min_fr`. We recommend setting dt such that `Neurons.max_fr * Agent.dt <<< 1`.
```
Neurons.plot_rate_timeseries(spikes=True)
Neurons.plot_ratemap(spikes=True)
```

#### Rate maps 
Place cells, grid cells and boundary vector cells have analytic firing rate functions. Their receptive fields can be displayed by querying their firing rate at an array of positions spanning the environment. This is done by: 
```
Neurons.plot_rate_map()
```
![](./figures/readme/rate_map.pdf)

An alternative, and more robust (in the case where neuronal firing rates aren't analytic) way to display the receptive field is to plot a heatmap of the positions of the Agent has visited weighted by the firing rate observed at each position. Over time, as coverage become complete, the firing fields become visible.
```
Neurons.plot_rate_map(by_history=True)
``` 
![](./figures/readme/rate_map_byhistory.pdf)

#### More complex Neuron types
We encourage more comlex Neuron classes to be made with the Neuron() class as parent. Specifically by writing your own `update()` and `get_state()` you can create more complex networks of neurons which, e.g. take weighted inouts of other neuronal layers, are recurrent, etc. By saving firingrate at each step plotting functions shown here should still be fucntional.  

## Tutorial 
A tutorial script can be found in scripts/ratinabox_tutorial.ipynb. This outlines most of the 

## Contribute 
RatInABox is an open source project, and we actively encourage community contributions. These can take various forms, such as new movement policies, new cells types, new geometries, bug fixes, documentation, citations of relevant work, or additional experiment notebooks. If there is a small contribution you would like to make, please feel free to open a pull request, and we can review it. If there is a larger contribution you are considering, please open a github issue. This way, the contribution can be discussed, and potential support can be provided if needed. 