---
layout: single
title: Moon and Magnetotail
header:
  teaser: moon_pos_1.png
collection: project
author_profile: true
share: true
---

**Identifying and Visualizing the Moon's Position with respect to the Earth's Magnetotail**

GitHub Repository: [Moon-and-Magnetotail](https://github.com/valerie-bernstein/Moon-and-Magnetotail)

DOI: [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5144032.svg)](https://doi.org/10.5281/zenodo.5144032)

This work contributed in a small way to the experimental design of the [Deep Space Radiation Genomics (DSRG)](https://www.colorado.edu/faculty/zea-luis/deep-space-radiation-genomics-dsrg-artemis-1) experiment on Artemis 1. The DSRG experiment will orbit the moon, and the goal of the experiment is to expose a collection of yeast to a space radiation environment similar to that expected for a trip beyond Earth's protective magnetospheric environment. This will enable the analysis of the effect of microgravity and space radiation in genes.

To ensure the experiment encounters 'pristine' solar wind conditions, it is important to avoid data collection during the spacecraft's (and consequently, the moon's) transit through the magnetotail. The solar wind interacts with the Earth's magnetic field to form the magnetosphere, which includes a long tail that acts as a barrier to energetic particles.

<p align="center">
  <img width="350" height="400" src="https://raw.githubusercontent.com/valerie-bernstein/personalwebsite.github.io/master/images/moon_orbit_graphic.png" alt="Moon Orbit Diagram">
</p>

<p align="center"><font size="1">Modified from Tim Stubbs/University of Maryland/GSFC, accessed from <a href="https://www.nasa.gov/topics/moonmars/features/magnetotail_080416.html">nasa.gov</a></font></p>

This is where our team - Valerie Bernstein (myself), Kaiya Wahl, and Delores Knipp - step in as space weather researchers! Our specific goal was to predict the position of the moon with respect to the magnetotail between given start and end dates in order to provide a yes/no decision table for data collection times.

It is important for the DSRG experiment to collect data for 3 days at a time and be conducted while in the free-flowing solar wind outside the magnetotail and bow shock. To accurately predict which times the experiment should or should not take place, the moon's position is calculated and compared to the position of the magnetotail and bow shock.

The Python astronomical ephemeris library [PyEphem](https://pypi.org/project/ephem/), developed by Rhodes (2011), is used to calculate the moon's right ascension (RA), declination (dec), and distance from the Earth given a specified date and time. PyEphem is also used to compute the moon phase at a specified time, which is given as the percentage of the moon's surface that is illuminated. We compute the moon's RA, dec, and phase every hour between the specified start and end dates. We also identify full moon times, when the surface illumination = 100%. Below is a plot of the moon phase from 2021 to 2024, where the red highlighted areas indicate the approximate 6 day windows in which the moon passes inside the magnetotail.

![Moon Phase Plot](https://raw.githubusercontent.com/valerie-bernstein/personalwebsite.github.io/master/images/moon_phase_plot.png)

To quantify the magnetotail transit times more precisely than using the 6-day window estimate, we covert the PyEphem-provided lunar position in RA and dec (which are Geocentric Equatorial Inertial, or GEI, coordinates) into Geocentric Solar Ecliptic (GSE) coordinates. This coordinate conversion is performed because the magnetotail lies in the Sun-Earth ecliptic plane. The coordinate conversion is provided by [M. Hapgood (1992)](https://www.sciencedirect.com/science/article/pii/003206339290012D) and outlined below.

![equation](https://latex.codecogs.com/gif.latex?%5Cbegin%7Bbmatrix%7D%20x%5C%5C%20y%5C%5C%20z%20%5Cend%7Bbmatrix%7D_%7BGSE%7D%20%3D%20%5Cmathbf%7BT%7D%20%5Cbegin%7Bbmatrix%7D%20x%5C%5C%20y%5C%5C%20z%20%5Cend%7Bbmatrix%7D_%7BGEI%7D)

In the above equation, square brackets indicate matrices, *x*, *y*, and *z* indicate cartesian vector components, and *T* is the transformation matrix. *T* is defined as the matrix multiplication of two rotation matrices:

![equation](https://latex.codecogs.com/gif.latex?%5Cmathbf%7BT%7D%20%3D%20%5B%5Clambda_%7B%5Codot%7D%2CZ%5D%20%5Ccdot%20%5B%5Cepsilon%2CX%5D)

where ![equation](https://latex.codecogs.com/gif.latex?%5B%5Clambda_%7B%5Codot%7D%2CZ%5D) is a rotation by the angle ![equation](https://latex.codecogs.com/gif.latex?%5Clambda_%7B%5Codot%7D) (the Sun's ecliptic longitude) about the z-axis, and ![equation](https://latex.codecogs.com/gif.latex?%5B%5Cepsilon%2C%20X%5D) is a rotation by the angle ![equation](https://latex.codecogs.com/gif.latex?%5Cepsilon) (the obliquity of the ecliptic) about the x-axis. These two rotations represent the rotation from Earth's equator to the ecliptic plane and the rotation in the ecliptic plane from the First Point of Aries to the Earth-Sun direction. The GSE coordinate are defined such that the +x direction is pointed from the Earth to the Sun, the +y direction is pointed towards dusk, and +z is in the northward direction out of the ecliptic plane. For further information about how to obtain the angle necessary to perform these rotations, please refer to [M. Hapgood (1992)](https://www.sciencedirect.com/science/article/pii/003206339290012D).

Given the moon's position in GSE coordinates, we find the intersection of the lunar orbit with the bounds of the magnetotail and bow shock in GSE coordinates. Since the moon's orbital radius is ~60![equation](https://latex.codecogs.com/gif.latex?R_E) in the ecliptic plane, the moon will intersect with the magnetotail at ~60![equation](https://latex.codecogs.com/gif.latex?R_E) in the negative x direction. Therefore for this analysis, the magnetotail and bow shock are drawn as 2D circles in the y-z plane at ![equation](https://latex.codecogs.com/gif.latex?x%20%3D%20-60R_E). The magnetotail circle is centered (in GSE x,y, and z components) at ![equation](https://latex.codecogs.com/gif.latex?%28x%3D-60R_E%2C%20y%3D%5CDelta%20y%2C%20z%3D0%29), with a radius of 30![equation](https://latex.codecogs.com/gif.latex?R_E). The position of the magnetotail is aberrated in the +y direction, and this aberration is defined as:

![equation](https://latex.codecogs.com/gif.latex?%5CDelta%20y%20%3D%20%7Cx_%7Bmoon%7D%7C%5Cfrac%7B29%7D%7BV_%7Bsw%7D%7D)

where ![equation](https://latex.codecogs.com/gif.latex?%7Cx_%7Bmoon%7D%7C) is the absolute value of the x-component of the moon's GSE position, and ![equation](https://latex.codecogs.com/gif.latex?V_%7Bsw%7D) is the solar wind velocity in units of km/s. This offset reflects the aberration of the magnetotail due to Earth's motion around the sun (average speed being 29 km/s) ([Hapgood, 2007](https://angeo.copernicus.org/articles/25/2037/2007/), and personal communication). We use a typical solar wind speed of 400 km/s. The bow shock circle is centered around the magnetotail with a radius of ~50![equation](https://latex.codecogs.com/gif.latex?R_E) ([Sibeck & Lin, 2014](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1002/2013JA019471)). The following plots show the moon's orbit in GSE coordinates over the course of 3 years as well as the 2D cross-sections of the magnetotail and bow shock (the inner and outer black circles).

![Moon Position 1](https://raw.githubusercontent.com/valerie-bernstein/personalwebsite.github.io/master/images/moon_pos_1.png)

![Moon Position 2](https://raw.githubusercontent.com/valerie-bernstein/personalwebsite.github.io/master/images/moon_pos_2.png)

In the above images, the Earth is located at (0,0,0). The purple data points represent the three days to be excluded from the "Yes" DSRG data collection dates prior to the moon's intersection with the bow shock. The yellow data points represent when the moon is transiting the bow shock, and the red data points represent when the moon is transiting the magnetotail. We also plot the moon phase over time, where the 3-day window prior to the moon entering the bow shock, the bow shock transit times, and the magnetotail transit times are all overlaid and distinguished by the same color scheme as the above plots.

![Moon Phase Extras](https://raw.githubusercontent.com/valerie-bernstein/personalwebsite.github.io/master/images/moon_phase_plot_extra_areas.png)

We can also display a zoomed-in view of this phase plot for a single lunar cycle (one month). This allows for a closer look at how much time the moon spends inside the bow shock and magnetotail in a month. Vertical lines indicate the times when the moon enters the regions of interest.

![Moon Phase Zoomed]https://raw.githubusercontent.com/valerie-bernstein/personalwebsite.github.io/master/images/moon_phase_plot_zoomed_in.png)

You can find all of the publications and presentations associated with this project under the *References* tab of this [ResearchGate Project Page](https://www.researchgate.net/project/Deep-Space-Radiation-Genomics-DSRG-Experiment-on-Artemis-1-Magnetotail-and-Bow-Shock-Effect).

