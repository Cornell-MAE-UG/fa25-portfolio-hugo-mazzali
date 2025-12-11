---
layout: project
title: Mechanics of Materials
description: Design, FEA, and iteration of a torque wrench
technologies: [Mechanics of Materials and Solids, Ansys FEA, Solidworks CAD]
image: /assets/images/RealCoverImage_FINAL_torque_wrench_3270.PNG
---

For my final project in my Mechanics of Materials course, I designed a torque wrench.

The goal of this project was to practice the design process by performing hand calculation based on operation conditions and then conducting FEA to iterate the design accordingly.

I began this design through analysis of a baseline design with baseline material properties outlines in the assignment. After this initial analysis, I made design changes by choosing a different material - 304 stainless steel - and different beam geometry. To pick a valid beam geometry, I performed hand calcs for both a circular and rectangular cross-sectioned beam. 

Under the "Analysis" section of this page you can find the specifc properties of 304 stainless steel from the Granta Database. Its Young's modulus of 28x10^6 gives the beam high stiffness, which is a necessary characteristic for this wrench, and both its yeild and tensiles strength of roughly 100 ksi and 150ksi respectively mean that the wrench can carry the desired load without permanent deformation. It is also important to note that the Young's modulus for 304 Stainless Steel sits in a sweet spot of not allowed the beam to deform permanently under the specified conditions, but also allows the beam to deflect enough to reach the baseline requirement of 1 mV/V measure at the strain gauge locations.

Below I summarize each design change and analysis results.

<div style="clear: both;"></div> 

<div class="section-header">Design Iterations</div>

<div class="project-row">
  <div class="project-text">
    <p>
      My first iteration on the base design was changing the material to 304 stainless steel. Though hand calculations using the material properties of this allow of steal, I was able to find square cross section that was smaller than the baseline design while still adhering the design requirments of safety factors and strain gauge outputs.
    </p>
    <p>
      The stainless steel was chosen as the material due to its strenth in yeilding, density, and material properties relating to rusting.
    </p>
  </div>

  <div class="project-image">
    <img
      src="{{ "/assets/images/Image_RECTANGLE_torque_wrench_3270.PNG" | relative_url }}"
      alt="Torque wrench engineering drawing"
    >
  </div>
</div>

<div class="project-row">
  <div class="project-text">
    <p>
      For my second iteration, I decided to analyse a cylindrical wrench because my hand-calculations had shown that it would have similar stresses under cantilevered point loading as the rectangular beam analysed, but with a more ergonomic profile.
    </p>
    <p>
      An issue I encountered with this design was the stress concentration and load distribution along the filleted edge of the drive. The drive in this design is an extrusion from a rounded face, which created geometric complications when modeling and refining the system mesh in Ansys. 
    </p>
  </div>

  <div class="project-image">
    <img
      src="{{ "/assets/images/Image_CYLINDER_torque_wrench_3270.PNG" | relative_url }}"
      alt="Torque wrench engineering drawing"
    >
  </div>
</div>

<div class="project-row">
  <div class="project-text">
    <p>
      I decided to make my final design a a primary geometry of a circular cross section, with the drive place on a flat section with ample fillet area at the end. 

    </p>
    <p>
      I chose a cylinder as my beam because it provides provides multi-directional strength for imperfect load cases experiences in real world applications and it also is a more ergonomic shape the user. I chose to combine the cylinder with a flat head profile to mitigate the stress concentrations found at the fillet through Ansys done on the solid cylindrical wrench design.
    </p>
  </div>

  <div class="project-image">
    <img
      src="{{ "/assets/images/CoverImage_FINAL_torque_wrench_3270.PNG" | relative_url }}"
      alt="Torque wrench engineering drawing"
    >
  </div>
</div>

<div style="clear: both;"></div>

<div class="section-header">Drawings with Dimensions</div>

<div class="three-image-row">

  <div class="image-with-caption">
    <img src="{{ "/assets/images/RECTANGLE_torque_wrench_Drawing_3270.PNG" | relative_url }}" 
         alt="Image 1"
         class="enlargeable">
    <p>Square Cross-Section Beam</p>
    <p>Click To Enlarge</p>
  </div>

  <div class="image-with-caption">
    <img src="{{ "/assets/images/CYLINDER_torque_wrench_Drawing_3270.PNG" | relative_url }}" 
         alt="Image 2"
         class="enlargeable">
    <p>Circle Cross-Section Beam</p>
    <p>Click To Enlarge</p>
  </div>

  <div class="image-with-caption">
    <img src="{{ "/assets/images/FINAL_torque_wrench_Drawing_3270.PNG" | relative_url }}" 
         alt="Image 3"
         class="enlargeable">
    <p>Final Cross-Section Beam</p>
    <p>Click To Enlarge</p>
  </div>
</div>

<div style="clear: both;"></div>

<div class="section-header">Hand Calculations</div>

<details>
  <summary><strong>View MATLAB Script for solid RECTANGULAR beam</strong></summary>

  <pre><code class="language-matlab">
% Solid beam of square cross section
% geometry
M = 600;           % applied moment [in-lbf]
L = 12;            % beam length [in]
P = M/L;           % equivalent end load [lbf]

b = 0.48;          % side length of square cross-section [in]
c_outer = b/2;     % distance from neutral axis to outer fiber [in]

gauge_x = 1.0;     % distance of gauge from fixed end [in]

% material properties - ranges not included
E   = 27.6e6;      % Young's modulus [psi]
nu  = 0.27;        % Poisson's ratio (not directly used)
Sy  = 100e3;       % yield strength [psi]
Su  = 152e3;       % tensile strength [psi] (not directly used)
Sf = 63.8e3;       % fatigue strength (using 10^7-cycles value as conservative) [psi]
KIC = 77.4e3;      % fracture toughness [psi*sqrt(in)]  (77.4 ksi*sqrt(in))

% cross-section properties for square
A = b^2;           % area [in^2]
I = b^4/12;        % moment of inertia for square [in^4]

% bending stress at outer fiber
max_stress = M * c_outer / I;   % [psi]
FOS_yield  = Su / max_stress;

% fatigue (using max_stress as alternating/conservative)
fatigue_stress = max_stress;
FOS_fatigue = Sf / fatigue_stress;

% crack calculations
a  = 0.04;                           % crack depth [in]
Sg = max_stress;                     % use max bending stress at crack location
KI = 1.12 * Sg * sqrt(pi * a);       % mode I stress intensity
FOS_fracture = KIC / KI;

% max deflection at free end of cantilever (load at end)
max_deflection = P * L^3 / (3 * E * I);  % [in]

% outputs at gauges
M_g = P * (L - gauge_x);             % moment at gauge location
stress_g = M_g * c_outer / I;        % stress at gauge [psi]
strain_g = stress_g / E * 1e6;       % microstrain
gauge_mVperV = strain_g / 1e3;       % assuming 1 mV/V per 1000 microstrain (as before)

% print results
fprintf('Max Stress: %.2f psi\n', max_stress);
fprintf('Factor of Safety (Yield): %.2f\n', FOS_yield);
fprintf('Factor of Safety (Fatigue): %.2f\n', FOS_fatigue);
fprintf('Factor of Safety (Fracture): %.2f\n', FOS_fracture);
fprintf('Max Deflection: %.6f in\n', max_deflection);
fprintf('Gauge Output (mV/V): %.2f mV/V\n', gauge_mVperV);
  </code></pre>
</details>

<details>
  <summary><strong>View MATLAB Script for solid CYLINDRICAL beam</strong></summary>

  <pre><code class="language-matlab">
%geometry for SOLID TUBE

M = 600;
L = 12;
P = M/L;

r = 0.275;
c = 1.0; 

%material properties - ranges not included
E   = 27.6e6;    % Young's modulus [psi]
nu  = 0.27;      % Poisson's ratio (not directly used)
Sy  = 100e3;     % yield strength [psi]
Su  = 152e3;     % tensile strength [psi] (not directly used)
Sf = 63.8e3;     % fatigue strength (using 10^7-cycles value as conservative) [psi]
KIC = 77.4e3;    % fracture toughness [psi*sqrt(in)]  (77.4 ksi*sqrt(in))

A = pi*r^2;

%calculations
I = pi*r^4/4; %for solid cylinder [in^4]
max_stress = M*r/I;
FOS_yield = Su/max_stress;

fatigue_stress = max_stress;
FOS_fatigue = Sf/fatigue_stress;

%crack calculations
a = 0.04;
Sg = max_stress;
KI = 1.12*Sg*sqrt(pi*a);
FOS_fracture = KIC/KI

max_deflection = P*L^3/(3*E*I);

%outputs at gauges
stress_g = P*(L-c)*(r)/I;
strain_g = stress_g/E*10^6;
gauge_mVperV = strain_g/10^3;

%print results
fprintf('Max Stress: %.2f psi\n', max_stress);
fprintf('Factor of Safety (Yield): %.2f\n', FOS_yield);
fprintf('Factor of Safety (Fatigue): %.2f\n', FOS_fatigue);
fprintf('Factor of Safety (Fracture): %.2f\n', FOS_fracture);
fprintf('Max Deflection: %.6f in\n', max_deflection);
fprintf('Gauge Output (mV/V): %.2f mV/V\n', gauge_mVperV);
  </code></pre>
</details>

<div class="three-image-row">

  <div class="image-with-caption">
    <img src="{{ "/assets/images/baseline_results.png" | relative_url }}" 
         alt="Image 1"
         class="enlargeable">
    <p>Baseline Design Results</p>
    <p>Click To Enlarge</p>
  </div>

  <div class="image-with-caption">
    <img src="{{ "/assets/images/circule_results.png" | relative_url }}" 
         alt="Image 2"
         class="enlargeable">
    <p>Circle Cross-Section Beam Results</p>
    <p>Click To Enlarge</p>
  </div>

  <div class="image-with-caption">
    <img src="{{ "/assets/images/square_results.png" | relative_url }}" 
         alt="Image 3"
         class="enlargeable">
    <p>Square Cross-Section Beam Results</p>
    <p>Click To Enlarge</p>
  </div>
</div>

<div style="clear: both;"></div>

<div class="section-header">Analysis</div>
![Granta Stainless Steel Properties]({{ "/assets/images/stainless_steel_properties.png" | relative_url }})

<div class="analysis-subsection">
  <div class="analysis-subtitle">Square Design</div>

  <div class="three-image-row">
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_normal_stress.png" | relative_url }}" alt="Baseline stress results" class="enlargeable">
      <p>Normal Stress</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_total_deformation.png" | relative_url }}" alt="Baseline deformation results" class="enlargeable">
      <p>Total Deformation</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_normal_elastic_strain.png" | relative_url }}" alt="Baseline safety factor results" class="enlargeable">
      <p>Elastic Strain</p>
    </div>
  </div>

  <div class="three-image-row">
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_strain_probe_location.png" | relative_url }}" alt="Baseline stress results" class="enlargeable">
      <p>Strain Probe</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_force_vector.png" | relative_url }}" alt="Baseline deformation results" class="enlargeable">
      <p>Force Vector</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_fixed_location.png" | relative_url }}" alt="Baseline safety factor results" class="enlargeable">
      <p>Fixed Drive</p>
    </div>
  </div>  

  <p class="analysis-text">
    Max normal stress: 77643 psi 
    Total deformation at load point: 0.23947 inches
    Strain at gauge location: 1.7e3 micro strain

    Shown in the "Max Normal Stress" figure, the maximum stress is occuring at tiny locations between the flat drive face and the fillet to the main body.  more realistic maximum normal strain can be selected from the contour plot average. Looking at the countour plot color distribution and the strain spectrum, a more realistic stress would be roughly 15000 psi.

    The maximum deformation is 0.23947 inches, which is consistant with the large bending moment being applied and is very close to the hand-calculated maximum deflection of 0.235885 inches. 

    The strain at the strain gauge location provides a strong signal for the bridge which meets the requirement of at least 1 mV/V. 

  </p>
</div>

<div class="analysis-subsection">
  <div class="analysis-subtitle">Cylindrical Design</div>

  <div class="three-image-row">
    <div class="image-with-caption">
      <img src="{{ "/assets/images/normal_stress.png" | relative_url }}" alt="Cylinder stress results" class="enlargeable">
      <p>Normal Stress</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/total_deformation.png" | relative_url }}" alt="Cylinder deformation results" class="enlargeable">
      <p>Total Deformation</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/normal_elastic_strain.png" | relative_url }}" alt="Cylinder safety factor results" class="enlargeable">
      <p>Normal Elastic Strain</p>
    </div>
  </div>

  <div class="three-image-row">
    <div class="image-with-caption">
      <img src="{{ "/assets/images/fixed_location.png" | relative_url }}" alt="Cylinder stress results" class="enlargeable">
      <p>Fixed Drive</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/force_vector.png" | relative_url }}" alt="Cylinder deformation results" class="enlargeable">
      <p>Force Vector</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/strain_probe_location.png" | relative_url }}" alt="Cylinder safety factor results" class="enlargeable">
      <p>Strain Probe</p>
    </div>
  </div>

  <p class="analysis-text">
    I conducted the same analysis on a circular design, but the results were not comparable because I was unable to solve a few issues: The fillet provided a complicated geometry that I was unable to finish optimizing at this time, resulting in very high stress concentrations at the filit discontinuities, and my Ansys was outputting a deformation that was a factor of 6 larger than what it should be according to my hand calculations and general intuition. I plan to continue to fix this design, but for the moment, I will chose the square profile as my final design iteration.
  </p>

</div>

<div class="section-header">Results Discussion</div>

For this design, I am choosing to use a half bridge with a bridge factor of 0.5 at a distance of 1 inch away from the center of the drive in the direction of the main body of the wrench where the strain is smooth and the bending moment is still high. This location is consistant with the probe location selected. Assumptions were confirmed with FEA.  The gauge will be aligned with the long axis of the handle. One gauge will be in tension and one will be in compression under the loading specified.

To calculate the torque wrench sensitivity: (Gauge Factor)x(Strain At Gauge)x(Bridge Factor) = (2)(1.7e3)(.5) = 1.7 mV/V. This sensitivity satisfies the design requirements. 

Below, I have included a contour plot of the maximum principle stress and a mesh refinment iteration. Note that my maximum principal stress is very high. This is likely because I made my mesh very fine around the fillet and context region of the drive. A more fine mesh will result in higher stress concentrations. 

<div style="clear: both;"></div>

  <div class="three-image-row">
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_max_principal_stress.png" | relative_url }}" alt="Cylinder stress results" class="enlargeable">
      <p>Max Principal Stress At Drive</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_mesh_refinement.png" | relative_url }}" alt="Cylinder deformation results" class="enlargeable">
      <p>Mesh Refinement</p>
    </div>
    <div class="image-with-caption">
      <img src="{{ "/assets/images/square_max_principal_stress_full.png" | relative_url }}" alt="Cylinder safety factor results" class="enlargeable">
      <p>Full Max Principal Stress</p>
    </div>
  </div>


<div style="clear: both;"></div>
