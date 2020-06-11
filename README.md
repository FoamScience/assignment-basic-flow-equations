# Assignment for the "Basic Equations, advective and diffusive transport" lecture

## Goals

## Table of Contents

## Basic-level skills

In the lecture, we have introduced end results of some advective/diffusive
OpenFOAM simulations. This Basic-Level section tries to get you to produce 
similar resuls on your own.

1. Clone our (Steady-State) introductory case from here (to your `run` 
directory):

```bash
git clone https://github.com/FOAM-School/res-eng-openfoam-intro diff-adv
```

2. Increase cells count to 20 in the x-direction 
   (check `constant/polyMesh/blockMeshDict`)

3. In `system/fvSchemes`, replace `steadyState` with `Euler` to approximate
   the time derivative using Euler's forward scheme.

4. Changing `ddtSchemes.default` entry to `Euler` is not enough! we need to
   adjust the numerical methods featured in `system/fvSolution` and some
   settings in `system/controlDict` as well:
   
   a. For the `T` variable, select an appropriate numerical solver (other than
   `smoothSolver`) with a decent preconditioner. Make it so the solver's
   tolerance is 1e-7 and no max iteration count is specified.

   b. Adjust `system/controlDict` settings so that we simulate the flow for 
   1.5 seconds, with an initial tiemstep of 0.001 seconds. The solver should
   write results to disk every 10 timesteps.

Now you should be able to run a sample simulation and check the results with
ParaView.

5. To explore the advective case, there are some changes that should be made:
  
   a. The boundary condition on `T` at the outlet patch should be `zeroGradient`.

   b. The diffusion coefficient in `constant/transportProperties` should be null.

   c. The `0/U` file should reflect an initial velocity of `(0.5 0 0)` in the
   whole domain.

   Run the case in that state; you should notice some oscillatory behaviour
   around 0.25 seconds.

6. From the previous question, we know that the advective term is causing
   instabilities. To fix the situation, we can, for example, select a more 
   "reliable" divergence schemes: In `system/fvSchemes`, pick 
   `limitedLinear 0.2` as the scheme used for `divSchemes.div(phi, T)` entry.

   The 0.2 value represents "by how much we limit the linear scheme". a value
   of 1 will result in no limitation.

   Run the simulation again and see for yourself if there is a stability gain.
  
7. Now to the diffusion case.

   a. Make the velocity null initially and assign 0.1 to the diffusion
   coefficient.

   b. The Laplacian term is approximated with `linear uncorrected` which is fine
   because we don't need any corrections for such simple meshes. Correcting
   operator approximations should be selected only in meshes with significant
   non-orthogonality angles. Run the simulation and check if it runs smooth.

## Intermediate-level skills

The general transport equation is the elementary block of most OpenFOAM solvers;
However, the equation we described in the lecture was the one used in the
`scalarTransportFoam` from the Foam-Extend package. Recent Mainline OpenFOAM
solvers add an interesting feature, called `fvOptions`.

1. Take a look at the equation at 
   [scalarTransportFoam.C, line 64 from OpenFOAM7 source code](https://github.com/OpenFOAM/OpenFOAM-7/blob/master/applications/solvers/basic/scalarTransportFoam/scalarTransportFoam.C#L62).
   How its transport equation is different from what we described in the
   lecture?

> `fvOptions` is a framework to include "user-defined" source terms into
> solved equation without re-compiling the solver.
> We sacrificed this framework when we chose to work with Foam-Extend-4 in
> this course. Because, it needs some programming experience, we will discuss
> it in Part 2 of this course.

2. Go back to our `adv-diff` case (from the basic-level section) and:
   
   a. Change the preconditioner for the numerical solver (Try diagonal, DILU,
   ... etc) and note the number of iterations per timestep required for
   convergence.

   b. Get the case into that "pure-advective" state (null diffusivity)
      then write a Shell script (Or a perl/Python script) that:
      
	  - Copies the case to a new directory multiple times
	  - Changes `fvSchemes.divSchemes.div(phi,T)` in each new case. Use the 
	    following (Gauss) schemes: linear, limitedLinear, filteredLinear and QUICK.
		(You need to figure out how these schemes are used first).
	  - Builds and checks the mesh
	  - Runs the simulation

> Hints for those who are new to Shell scripting:
> - A shell script is a just a group of shell commands
> - Execute `help for` in a BASH shell to figure out how looping is supposed to
>   work.
> - `cp -r` copies a directory
> - `sed` is the most basic tool for "search-and-replace" operations on Unix
>   files
> - Then you can run OpenFOAM commands the usual way.

At this point, we lack the necessary post-processing skills to make anything
useful from this script, but we'll learn those skills in no time!
For now, you can figure out which scheme works best by going opening each case
in ParaView and inspecting how the scheme performed.

## Advanced-level skills

No advanced-level questions until you complete the whole module.
