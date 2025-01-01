---
layout: post
title: "Deriving a shell model of turbulence  (Part 1, Updated)"
permalink: /_posts/2024-10-20-shell-model-of-turbulence1 -
author: Florian Heyder
readtime: 15min
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

We approximate the fluid velocity, the fluid pressure and the body forces using the following Fourier decompositions:

$$
\begin{align}
    \mathbf{u}(\mathbf{x}, t) = \sum\limits_{\mathbf{k}} \hat{\mathbf{u}}_{\mathbf{k}}(t) \exp\left( i\mathbf{k} \cdot \mathbf{x}\right),\\
    p(\mathbf{x}, t) = \sum\limits_{\mathbf{k}} \hat{p}_{\mathbf{k}}(t) \exp\left( i\mathbf{k} \cdot \mathbf{x}\right),\\
    \mathbf{f}(\mathbf{x}, t) = \sum\limits_{\mathbf{k}} \hat{\mathbf{f}}_{\mathbf{k}}(t) \exp\left( i\mathbf{k} \cdot \mathbf{x}\right),
\end{align}
$$

where symbols with a hat denote the time-dependent Fourier coefficients and \\(\mathbf{k}\\) are the wavenumbers. In the shell model approximation, these wavenumbers will be restricted to a subset of applicable wavenumbers \\(K\\). For now, we will not concern ourselves with which \\(k\\) are admissible, but assume an infinite amount of wavenumbers.

The next steps are clear: we will plug the above approximation into the Navier-Stokes equations. Furthermore, we will eliminate most of the sums over the wavevectors by employing one of the most commonly used tricks in physics: 
- Multiply the equation by \\( \exp\left( -i\mathbf{k}' \cdot \mathbf{x}\right) \\). 
- Integrate over the domain \\(\Omega\\).
- This will leave a \\(\delta_{kk'}\\) in the summation, i.e., isolate the coefficients with wavenumber \\(\mathbf{k}'\\).
- Finally, rename \\(\mathbf{k}'\\) to \\(\mathbf{k}\\).

The last steps confused me because I often conflated the resulting \\(\mathbf{k}\\) with the initial \\(\mathbf{k}\\) of the Fourier Ansatz. This confusion arises from textbooks and professors who never clarify that the last step is simply a renaming of variables for convenience. Let us continue with the same convention while being aware of what we are doing. This leaves us with the following equation:

$$
\frac{\partial \hat{\mathbf{u}}_\mathbf{k}}{\partial t} = -i\sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}}(\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{\mathbf{u}}_\mathbf{q_2} - i \mathbf{k} \hat{p}_{\mathbf{k}} - \nu |\mathbf{k}|^2 \hat{\mathbf{u}}_\mathbf{k} + \hat{\mathbf{f}}_\mathbf{k}.
$$

The most complicated term is clearly the nonlinearity of \\((\mathbf{u} \cdot \nabla) \mathbf{u}\\), which is fundamentally related to the essence of turbulence. We can observe that this nonlinearity in real space becomes an interaction of different Fourier coefficients, i.e., of different wavenumbers. The nonlinearity leads to a mixing effect that causes interactions between different wavenumbers \\(\mathbf{q_1},\mathbf{q_2}\\) and therefore between different spatial dimensions (remember that the wavenumbers can be described by the wavelength \\(\lambda\\) as \\(\|\mathbf{k}\| = \frac{2\pi}{\lambda}\\)). Remember, that the terms \\(\hat{p}_\mathbf{k}\\) and \\(\hat{\mathbf{f_k}}\\) are the corresponding Fourier coefficients of pressure and body forces, respectively.

As we consider an incompressible flow, the pressure is fully determined by the velocity field (sometimes also denoted as 'enslaved' by the velocity field). This can be shown by applying \\(\nabla \cdot \\) from the left on the Navier-Stokes equations in real space and making use of the mass balance:

$$
\nabla^2 p = -\nabla \cdot \left[(\mathbf{u} \cdot \nabla) \mathbf{u}\right] + \nabla \cdot \mathbf{f}.
$$

At this point, let us introduce a constraint to make the derivation more easy: by assuming a divergence-free forcing, the last term drops out. Let us not forget this assumption in later steps! There are many examples where the forcing term is in fact not divergence free, like in thermal convection. However this will just result in one additional term in for the pressure Laplace equations. Finally, we end up with:

$$
\nabla^2 p = -\nabla \cdot \left[(\mathbf{u} \cdot \nabla) \mathbf{u}\right].
$$

We can describe this using the Fourier expansion again. Moreover, we can isolate a certain wavenumber, using the trick described above, and solve for \\(p_\mathbf{k}\\):

$$
\hat{p}_\mathbf{k} = \frac{i \mathbf{k}}{|\mathbf{k}|^2} \sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{\mathbf{u}}_\mathbf{q_2}.
$$

Therefore, the pressure contribution also introduces a mixing term into the equation of motion for \\(\hat{\mathbf{u}}_\mathbf{k}\\).

Now let's summarizes what we have for now.



### Intermediate Summary
The equations of motion for an incompressible Newtonian flow are given by:

$$
\begin{align}
\frac{\partial \hat{\mathbf{u}}_\mathbf{k}}{\partial t} &= -i\sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{\mathbf{u}}_\mathbf{q_2} - i \mathbf{k} \hat{p}_{\mathbf{k}} - \nu |\mathbf{k}|^2 \hat{\mathbf{u}}_\mathbf{k} + \hat{\mathbf{f}}_\mathbf{k},\\
\mathbf{k} \cdot \hat{\mathbf{u}}_\mathbf{k} &= 0,
\end{align}
$$

where

$$
\hat{p}_\mathbf{k} = \frac{i \mathbf{k}}{|\mathbf{k}|^2} \sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{\mathbf{u}}_\mathbf{q_2}.
$$

The mass balance in Fourier space is given by:

$$
\mathbf{k} \cdot \hat{\mathbf{u}}_\mathbf{k} = 0.
$$

Finally, the physical fields must be real. This requires:

$$
\hat{\mathbf{u}}_\mathbf{-k} = \hat{\mathbf{u}}_\mathbf{k}^*,
$$

which halves the number of equations that need to be solved!

### Ordering the nonlinear terms
The nonlinearities in the momentum balance can be ordered and rewritten. To make things easier, let us define some useful variables  

$$
\begin{align} 
U_{\mathbf{k},x_i,x_j}      &= \sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} \hat{u}_{\mathbf{q_1},x_i} \hat{u}_{\mathbf{q_2},x_j} = U_{\mathbf{k},x_j,x_i}\\[.15cm]
w_{\mathbf{k},x} &= i(k_x U_{\mathbf{k},x,x}+k_y U_{\mathbf{k},x,y}+k_z U_{\mathbf{k},x,z})\\[.15cm]
w_{\mathbf{k},y} &= i(k_x U_{\mathbf{k},y,x}+k_y U_{\mathbf{k},y,y}+k_z U_{\mathbf{k},y,z})\\[.15cm]
w_{\mathbf{k},z} &= i(k_x U_{\mathbf{k},z,x}+k_y U_{\mathbf{k},z,y}+k_z U_{\mathbf{k},z,z})
\end{align}
$$

With this we can rewrite the inertial and pressure contributions for the x-components in the following way

$$
\begin{align} 
-i\sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{u}_{\mathbf{q_2},x} - i k_x \left[\frac{i }{|\mathbf{k}|^2}\mathbf{k}\cdot \sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{\mathbf{u}}_\mathbf{q_2}\right]&= -\frac{k_y^2+k_z^2}{|\mathbf{k}|^2}w_{\mathbf{k},x} + \frac{k_xk_y}{|\mathbf{k}|^2}w_{\mathbf{k},y} + \frac{k_xk_z}{|\mathbf{k}|^2}w_{\mathbf{k},z}\\
&=-i\frac{k_y^2+k_z^2}{|\mathbf{k}|^2}k_x U_{\mathbf{k},x,x} +i\frac{k_x k_y^2}{|\mathbf{k}|^2}U_{\mathbf{k},y,y}+i\frac{k_xk_z^2}{|\mathbf{k}|^2} U_{\mathbf{k},z,z}\\
&+i\frac{k_x^2-k_y^2-k_z^2}{|\mathbf{k}|^2}k_y U_{\mathbf{k},x,y} +i\frac{k_x^2-k_y^2-k_z^2}{|\mathbf{k}|^2}k_z U_{\mathbf{k},x,z} +2i\frac{k_xk_yk_z}{|\mathbf{k}|^2} U_{\mathbf{k},y,z}
\end{align}
$$
The same can be done for the \\(y\\) and \\(z\\) component of the momentum balance. These expressions can be written in a more compact form using the coupling strength tensor \\(M_{\mathbf{k},lmn}\\)

$$
M_{\mathbf{k},lmn} = \frac{1}{2}\left[k_{x_m} P^\perp_{ln}+k_{x_n} P^\perp_{lm} \right]\quad \text{with} \quad P_{lr} = \left(\delta_{lr} - \frac{k_{x_l}k_{x_r}}{|\mathbf{k}|^2}\right)
$$

where \\(P^\perp_{ln}\\) and \\(P^\perp_{ln}\\) are orthogonal projectors and \\(l,m,n = 1,2,3\\). This shortens the expression for the nonlinearities in the \\(x_l\\)-component of the Navier-Stokes equations

$$
\begin{align} 
-i\sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{u}_{\mathbf{q_2},x_l} - i k_{x_l} \left[\frac{i }{|\mathbf{k}|^2}\mathbf{k}\cdot \sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} (\hat{\mathbf{u}}_\mathbf{q_1} \cdot \mathbf{k}) \hat{\mathbf{u}}_\mathbf{q_2}\right]&= - iM_{\mathbf{k},lmn} \sum\limits_{\substack{\mathbf{q_1},\mathbf{q_2} \\ \mathbf{q_1}+\mathbf{q_2}=\mathbf{k}}} \hat{u}_{\mathbf{q_1},x_m} \hat{u}_{\mathbf{q_2},x_n}. 
\end{align}
$$

In summary this leaves us with the follwing expression for the momentum balance in spectral space

$$
\begin{align}
\boxed{
\frac{\partial \hat{u}_{\mathbf{k},x_l}(t)}{\partial t} =  - iM_{\mathbf{k},lmn} \sum\limits_{\mathbf{q}} \hat{u}_{\mathbf{q},x_m}(t) \hat{u}_{\mathbf{k}-\mathbf{q},x_n}(t) - \nu |\mathbf{k}|^2 \hat{u}_{\mathbf{k},x_l}(t) + \hat{f}_{\mathbf{k},x_l}(t)}  
\end{align}
$$

where in a last step we have eliminated the second index \\(\mathbf{q_2}\\) and renamed \\(\mathbf{q_1}\\) to \\(\mathbf{q}\\)

### Conclusion
We have derived a compact expression for the Navier-Stokes equations, specifically the momentum balance for Newtonian, incompressible flow. The nonlinearities transform into a coupling term that mixes Fourier coefficients of different wavenumbers, providing a new perspective on how energy is transferred through cascades from large to small scales (or vice versa) in turbulent flows. In this derivation, we assumed a divergence-free forcing of the flow, such as a stirring at the largest scales, which will be introduced in subsequent posts. However, the derivation changes only slightly when considering a non-divergence-free forcing, such as thermal convection, where a temperature field enters the equation.

### Outlook
For now, the sums over the wavenumbers run over an infinite set of wavenumbers, kk, as the coupling term introduces new wavenumbers that affect the flow at each fixed wavenumber. This makes solving these equations cumbersome, as infinite couplings have to be considered. However, we might expect these scale interactions to weaken as the interacting wavenumbers become further apart from each other.

In the next posts, I will elaborate on the case when we limit the wavevectors to a set of admissible wavenumbers that grow exponentially in wavenumber, while allowing only certain interactions which lead to wavenumbers that are in the initial wavenumber set.
