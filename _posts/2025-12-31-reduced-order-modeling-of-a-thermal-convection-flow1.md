---
layout: post
title: "Reduced order modeling of thermal convection flows (Part I): Introduction"
permalink: /_posts/2025-12-31-reduced-order-modeling-of-a-thermal-convection-flow1
author: Florian Heyder
readtime: 15
tags: [natural convection, thermal convection, heat transfer, reduced order modeling, ROM, proper orthogonal decomposition, POD, fluid dynamics, fluid mechanics, fluid flow, physics]
thumbnail-img: /assets/img/2025-12-31-reduced-order-modeling-of-a-thermal-convection-flow1/thumbnail.jpg
cover-img: /assets/img/2025-12-31-reduced-order-modeling-of-a-thermal-convection-flow1/cover.jpg
---

### Introduction
Thermal convection is a fundamental process that shapes many aspects of our daily lives, engineering systems, and natural phenomena. Whether it’s the steam rising from your morning coffee, the cooling systems of nuclear reactors, or the towering clouds forming in the atmosphere — all these processes are governed by the same underlying principle: the transport of heat through a fluid.

In each case, a fluid (like coffee, cooling water, or moist air) moves heat from a warmer region to a cooler one. For example, in the atmosphere, warm air near the ground rises and carries heat to higher, cooler altitudes. Understanding and accurately modeling these convection processes is crucial for predicting and controlling heat transfer — a challenge that spans many scientific and engineering disciplines.

This raises practical and fascinating questions such as:

- How long do I need to blow on my hot coffee before it’s cool enough to drink?    
- What temperature and flow rate should the cooling water have to safely operate a nuclear reactor?    
- At what altitude do clouds begin to form, and how does temperature change with height?

<div style="text-align: center;">

<a href="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/intro-examples.png" data-lightbox="example-set">
  <img src="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/intro-examples.png" data-title="Figure 1." width="1000" style="display: block; margin: auto;">
</a>
</div>
Fig. 1 - <strong>Convective flows in everyday life</strong>:
<em>a) A cup of hot coffee cools down as heat moves from the liquid to the surrounding air. Warm air rises from the surface, carrying the heat away. 
b) Cumulus clouds over Frankfurt am Main, Germany. These clouds form when warm, moist air rises and cools. As the air reaches colder layers, the moisture condenses into clouds. The flat cloud base shows where this condensation starts.
</em>


The simplest way to answer some of these questions is through direct experimentation. For example, you can measure how quickly your coffee cools by using a thermometer and a small fan. But not every problem is so easily or cheaply tested. Consider an engineering challenge such as evaluating the efficiency of a nuclear reactor’s cooling system. Clearly, it's not feasible to rely on trial and error—starting with a rough design, testing it, and adjusting afterward. Instead, a robust and reliable design must be developed a priori, before any physical implementation. Another example is cloud formation: to understand it in detail, you’d need a dense network of sensors, aircraft, or satellites to measure temperature, humidity, and motion at different altitudes — a complex and expensive undertaking.

That’s where numerical simulations come into play. Instead of relying solely on measurements, we can simulate these processes using physical models developed by scientists over the past decades. These simulations provide a cost-effective and scalable alternative, allowing us to explore scenarios that would be difficult or impossible to measure directly — whether it's in a laboratory, a nuclear power plant, or the upper troposphere.

To better understand the trade-offs in numerical simulations, it's useful to distinguish between different modeling approaches — most notably Direct Numerical Simulation (DNS) and more approximate methods like Large Eddy Simulation (LES) or Reynolds-Averaged Navier–Stokes (RANS).

<div style="text-align: center;">
<a href="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/LES-vs_DNS.png" data-lightbox="example-set">
  <img src="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/LES-vs-DNS.png" data-title="Figure 2." width="1000">
</a>
</div>
Fig.2 - <strong>Concept of two simulation methods: Direct Numerical Simulation (DNS) and Large Eddy Simulation (LES) and Reynolds-Averaged Navier–Stokes (RANS)</strong>: 
<em>
a) DNS resolves all flow scales, including the smallest turbulent structures, resulting in very high computational cost. b) LES resolves large eddies while modeling smaller scales, reducing cost while preserving key flow features. c) RANS models all turbulence through time-averaging, capturing only the mean flow at minimal computational expense.
</em>

### The Bottleneck of Modeling Turbulence

The former resolved all spatial and temporal scales of motion in a fluid flow. That is from the smallest turbulent eddies to the largest flow structures. This, however, comes at an enormous computational cost. The key challenge lies in the Reynolds number (Re), a dimensionless quantity characterizing the ratio of inertial to viscous forces in a flow. As the Reynolds number increases, the range of active scales in the flow widens significantly. To fully resolve all turbulent scales in three dimensions, the number of spatial grid points required scales as

$$
N = N_x \times N_y \times N_z \sim Re^{9/4},
$$

i.e. stronger than quadraticly!

This steep scaling arises because the smallest scales (Kolmogorov scales) shrink with increasing Re, and resolving them in all three spatial dimensions demands increasingly finer numerical meshes. Moreover, the time-stepping scheme must resolve the fastest (smallest-scale) dynamics, which further multiplies the cost.

To give you an idea what fluid dynamisists have to deal with: the Reynolds number in atmospheric convection can reach values on the order of \\(10^{8} - 10^{10}\\). Plugging such values into the scaling relation yields a required number of grid points on the order of \\(N \sim  10^{18}\\) or more — far beyond the capabilities of even the largest supercomputers today. To make this point clear: assuming one grid point requires 32 bit = 4 byte this would translate into 1EB (Exabyte), i.e. one million Terabyte! This makes DNS entirely infeasible for large-scale systems like the Earth's atmosphere, as well as most engineering systems. 

<div style="text-align: center;">
<a href="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/DNS-cost.png" data-lightbox="example-set">
  <img src="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/DNS-cost.png" data-title="Figure 3." width="1000">
</a>
</div>
Fig.3 - <strong>Computational cost of Direct Numerical Simulation (DNS):</strong>: 
<em>
The number of required grid points—and thus memory consumption—scales steeply with Reynolds number and system dimensionality. For 3D flows, DNS becomes prohibitively expensive at high Reynolds numbers.
</em>

For this purpose less computationally intensive models are commonly used in engineering. Usually it is not necessary to resolve all eddies for determining the main flow features. Traditional approaches like Large Eddy Simulation or Reynolds-Averaged Navier–Stokes models require significant less computational resources, but often depend on external parameters or experimental measurements that are used to gauge the numerical data with the real world. However, such data may not always be readily available to the engineers.

Right now fluid dynamicists that make use of numerical flow simulations are caught in the middle: on the one hand simulating every detail but being restrained by the computation ressources on the other being dependent on external or assumed parameters in unprecise models while retaining compuational runtime to a feasible amount. 

This is where the current developments in machine learning enter the picture. In order to overcome some of the above mentioned limitations, trained machine learning models might be a good surrogate for computing highly resolved flow features while lowering computational costs. One of these approaches will be the topic of this blog series: reduced-order model (ROMs), as well a predictor model (e.g. a recurrent neural network).

By building reduced-order models (ROMs), we can capture the essential dynamics of convection while dramatically reducing the computational cost. On top of that, predictive models — such as reservoir computing or neural networks — can learn to forecast system behavior based on previous states.

By combining ROMs with predictive models, we move towards powerful hybrid systems that can act as recommender engines or even digital twins. These tools not only enable real-time monitoring of complex systems, but also allow for fast exploration of alternative scenarios — for instance, how the system might react to a change in boundary conditions or operational parameters. This opens the door to smarter control, design optimization, and safer operation across a wide range of engineering applications.


### Reduced Order Models: How machine learning models may help

Fluid flows, especially turbulent ones, are notoriously complex. If you’ve ever watched a simulation or experiment of swirling eddies, chaotic jets, or unsteady wakes, you know how overwhelming the motion can be. What if there were a way to simplify all of this — to extract the essence of the flow and describe it using just a few key patterns? That’s exactly what Proper Orthogonal Decomposition, or POD, allows us to do.

At its core, POD is a mathematical technique that helps identify the most important features within a fluid flow. It does this by analyzing a series of flow field snapshots taken over time and finding the dominant structures — the ones that occur most often or carry the most energy. In other words, it breaks down a complex flow into a set of spatial patterns, called modes, which together represent the key dynamics of the system.

To get started with POD, we first collect data from the flow — typically velocity fields at different time steps. The method then processes this data to find patterns that are common across the snapshots. These patterns are ranked according to how much of the total energy or variance in the flow they capture. The first mode usually represents the most dominant feature, such as the mean flow structure. Subsequent modes capture progressively smaller but still important variations, like large-scale vortices or periodic oscillations.

One of the most powerful aspects of POD is that it often takes only a handful of modes to reconstruct the flow with high accuracy. For instance, in a turbulent flow, it’s not uncommon for just the first five or ten modes to capture most of the system’s energy. This means that instead of working with millions of data points, we can focus on a small number of meaningful structures. This reduction makes it easier to understand what drives the flow, to diagnose instabilities, or to develop control strategies.

In practical terms, POD is widely used in areas like aerodynamics, combustion, and process engineering. Engineers use it to design reduced-order models that simulate or control flows more efficiently. For example, in a channel flow with turbulent behavior, POD might reveal that most of the flow dynamics can be captured by just three modes: one corresponding to the base flow and two others representing coherent oscillations or vortices. With this information, engineers can develop targeted interventions without needing to account for every detail of the turbulence.

Proper Orthogonal Decomposition thus serves as a kind of lens — helping us see through the complexity of fluid motion and focus on the essential mechanisms at play. By breaking a flow down into its fundamental building blocks, POD offers not only insight but also powerful tools for modeling, analysis, and control.

While POD is a powerful linear method, it can struggle with capturing complex nonlinear dynamics present in many real-world flows. This is where autoencoder neural networks come into play — they can learn similar low-dimensional representations as POD, but in a nonlinear way, making them capable of capturing more intricate flow features. Just like POD, these autoencoders can be used to build efficient reduced-order models, offering a modern, data-driven alternative for simulating and controlling fluid flows.

### Pipeline for a machine learning model in fluid dynamics

<div style="text-align: center;">
<a href="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/ML-pipeline.svg" data-lightbox="example-set">
  <img src="/assets/img/2025-05-25-reduced-order-modeling-of-a-thermal-convection-flow1/ML-pipeline.svg" data-title="Figure 5." width="1000">
</a>
</div>
Fig.5 - <strong>Machine Learning Model Pipeline:</strong>: 
<em>
</em>


### Next Up: ROM of a thermal convection flow
