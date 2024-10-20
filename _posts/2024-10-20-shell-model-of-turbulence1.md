---
layout: post
title: "Deriving a shell model of turbulence  (Part 1)"
permalink: /_posts/2024-10-20-shell-model-of-turbulence1 -
author: Florian Heyder
readtime: 6min
tags: [turbulence, fluid dynamics, fluid mechanics, physics]
thumbnail-img: /assets/img/2024-10-20-shell-model-of-turbulence1/thumbnail.jpg
cover-img: /assets/img/2024-10-20-shell-model-of-turbulence1/cover.jpg
---

### Introduction
During my PhD, I became increasingly fascinated by the phenomenon of turbulence—the last unresolved puzzle of classical physics, as no unified theory fully describes turbulent fluid motion. Now, working as an engineer in a more practical role, turbulence is less of a challenge I regularly face. In engineering, we often don't deal with turbulence, such as in highly viscous battery slurries, or we actively work to avoid it, as in pipe flow design, where minimizing pressure losses is crucial. Still, having a solid grasp of turbulence and its physical implications is valuable for any engineer.   

When I reflect on my studies of turbulence, I often recall the shell models, which were instrumental in helping me grasp the underlying physical principles. In this post, I want to derive the basic equations of motion that describe these shell models. We will begin with the Navier-Stokes equations in spectral space. In future posts, I will utilize this Fourier representation to derive one of the many shell models of turbulence. Furthermore, I will illustrate what we can learn about turbulent flows from these models. Additionally, the Fourier representation of the equations of fluid motion provides a good starting point for direct numerical simulations (DNS), where turbulence is numerically fully resolved.

### The Navier-Stokes equations in Spectral Space
As mentioned, we start with the momentum balance in spectral space. For an incompressible Newtonian flow with fluid velocity \\(\mathbf{u} = (u_x, u_y, u_z)^T\\), kinematic pressure \\(p\\) (i.e., pressure divided by density), and kinematic viscosity \\(\nu\\), the Navier-Stokes equations are   

$$
 \frac{\partial \mathbf{u}}{\partial t}+(\mathbf{u} \cdot \nabla) \mathbf{u} = -\nabla p + \nu \nabla^2 \mathbf{u} + \mathbf{f},   
$$

where \\(\mathbf{f}\\) represents any body force acting on the fluid. This might be a Boussinesq term that allows for thermal convection or a stirring force that adds energy to the fluid system.

The mass balance is given by:

$$
\nabla \cdot \mathbf{u} = 0.
$$

As we are interested in the phenomenon of turbulence, let us, for simplicity, assume a periodic cubic box \\(\Omega = [0, 2\pi L]^3\\), where the fluid is not disturbed by any boundaries. Note that this contrasts with practical engineering problems, where walls and their geometry play a crucial role. Furthermore, by employing periodic boundaries, any fluid leaving one side of the domain will enter from the other side.

We approximate the fluid velocity using the following Fourier decomposition:

$$
\begin{align}
    \mathbf{u}(\mathbf{x}, t) = \sum\limits_{\mathbf{k}} \hat{\mathbf{u}}_{\mathbf{k}}(t) \exp\left( i\mathbf{k} \cdot \mathbf{x}\right),
\end{align}
$$

where \\(\hat{\mathbf{u}}_\mathbf{k}\\) are the time-dependent Fourier coefficients and \\(\mathbf{k}\\) are the wavenumbers. In the shell model approximation, these wavenumbers will be restricted to a subset of applicable wavenumbers \\(K\\). For now, we will not concern ourselves with which \\(k\\) are admissible, but assume an infinite amount of wavenumbers.

The next steps are clear: we will plug the above approximation into the Navier-Stokes equations. Furthermore, we will eliminate most of the sums over the wavevectors by employing one of the most commonly used tricks in physics: 
- Multiply the equation by \\( \exp\left( -i\mathbf{k}' \cdot \mathbf{x}\right) \\). 
- Integrate over the domain \\(\Omega\\).
- This will leave a \\(\delta_{kk'}\\) in the summation, i.e., isolate the coefficients with wavenumber \\(\mathbf{k}'\\).
- Finally, rename \\(\mathbf{k}'\\) to \\(\mathbf{k}\\).

The last steps confused me because I often conflated the resulting \\(\mathbf{k}\\) with the initial \\(\mathbf{k}\\) of the Fourier Ansatz. This confusion arises from textbooks and professors who never clarify that the last step is simply a renaming of variables for convenience. Let us continue with the same convention while being aware of what we are doing. This leaves us with the following equation:

$$
\frac{\partial \hat{\mathbf{u}}_\mathbf{k}}{\partial t} = -\sum\limits_{\mathbf{k}'} (\hat{\mathbf{u}}_{\mathbf{k}-\mathbf{k}'} \cdot \mathbf{k}') \hat{\mathbf{u}}_\mathbf{k'} - i \mathbf{k} \hat{p}_{\mathbf{k}} - \nu |\mathbf{k}|^2 \hat{\mathbf{u}}_\mathbf{k} + \hat{\mathbf{f}}_\mathbf{k}.
$$

The most complicated term is clearly the nonlinearity of \\((\mathbf{u} \cdot \nabla) \mathbf{u}\\), which is fundamentally related to the essence of turbulence. We can observe that this nonlinearity in real space becomes an interaction of different Fourier coefficients, i.e., of different wavenumbers. The nonlinearity leads to a mixing effect that causes interactions between different spatial dimensions (remember that the wavenumbers can be described by the wavelength \\(\lambda\\) as \\(\|\mathbf{k}\| = \frac{2\pi}{\lambda}\\)). The terms \\(\hat{p}_\mathbf{k}\\) and \\(\hat{\mathbf{f_k}}\\) are the corresponding Fourier coefficients of pressure and body forces, respectively (I implicitly assumed a Fourier Ansatz for them as well at the beginning).

As we consider an incompressible flow, the pressure is fully determined by the velocity field (sometimes also denoted as 'enslaved' by the velocity field). This can be shown by applying \\(\nabla \cdot \\) from the left on the Navier-Stokes equations in real space and making use of the mass balance:

$$
\nabla^2 p = -\nabla \cdot \left[(\mathbf{u} \cdot \nabla) \mathbf{u}\right] + \nabla \cdot \mathbf{f}.
$$

This can be simplified by assuming divergence-free forcing, as we will do now. Let us not forget this in later steps! We then end up with:

$$
\nabla^2 p = -\nabla \cdot \left[(\mathbf{u} \cdot \nabla) \mathbf{u}\right].
$$

We can describe this using the Fourier expansion again. Moreover, we can isolate a certain wavenumber, using the trick described above, and solve for \\(p_\mathbf{k}\\):

$$
\hat{p}_\mathbf{k} = \frac{i \mathbf{k}}{|\mathbf{k}|^2} \sum\limits_{\mathbf{k}'} (\hat{\mathbf{u}}_{\mathbf{k}-\mathbf{k}'} \cdot \mathbf{k}') \hat{\mathbf{u}}_\mathbf{k'}.
$$

Therefore, the pressure contribution also introduces a mixing term into the equation of motion for \\(\hat{\mathbf{u}}_\mathbf{k}\\). In this way, the momentum balance can be fully described.

The mass balance in Fourier space is given by:

$$
\mathbf{k} \cdot \hat{\mathbf{u}}_\mathbf{k} = 0.
$$

Finally, the physical fields must be real. This requires:

$$
\hat{\mathbf{u}}_\mathbf{-k} = \hat{\mathbf{u}}_\mathbf{k}^*,
$$

which limits halves the number of equations that need to be solved!

### Summary
The equations of motion for an incompressible Newtonian flow are given by:

$$
\begin{align}
\frac{\partial \hat{\mathbf{u}}_\mathbf{k}}{\partial t} &= -\sum\limits_{\mathbf{k}'} (\hat{\mathbf{u}}_{\mathbf{k}-\mathbf{k}'} \cdot \mathbf{k}') \hat{\mathbf{u}}_\mathbf{k'} - i \mathbf{k} \hat{p}_{\mathbf{k}} - \nu |\mathbf{k}|^2 \hat{\mathbf{u}}_\mathbf{k} + \hat{\mathbf{f}}_\mathbf{k},\\
\mathbf{k} \cdot \hat{\mathbf{u}}_\mathbf{k} &= 0,
\end{align}
$$

where

$$
\hat{p}_\mathbf{k} = \frac{i \mathbf{k}}{|\mathbf{k}|^2} \sum\limits_{\mathbf{k}'} (\hat{\mathbf{u}}_{\mathbf{k}-\mathbf{k}'} \cdot \mathbf{k}') \hat{\mathbf{u}}_\mathbf{k'},
$$

and 

$$
\hat{\mathbf{u}}_\mathbf{-k} = \hat{\mathbf{u}}_\mathbf{k}^*.
$$

In the next posts, I will elaborate on the case when we limit the wavevectors to a set of admissible wavenumbers that grow exponentially in wavenumber, while allowing only certain interactions which lead to wavenumbers that are in the initial wavenumber set.
