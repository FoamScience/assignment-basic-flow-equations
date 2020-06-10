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

  b. The laplacian term is approximated with `linear uncorrected` which is fine
  because we don't need any corrections for such simple meshes. Correcting
  operator approximations should be selected only in meshes with significant
  non-orthogonality angles. Run the simulation and check if it runs smooth.

## Intermediate-level skills

## Advanced-level skills
