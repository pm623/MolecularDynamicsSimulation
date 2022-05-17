<!DOCTYPE html>
<!-- saved from url=(0141)file:///C:/Users/mpran/Documents/GitHub/MolecularDynamicsSimulation/docs/Welcome%20to%20GitHub%20Pages%20_%20MolecularDynamicsSimulation.html -->
<html lang="en-US"><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
    <link rel="stylesheet" href="./index_files/style.css" media="screen" type="text/css">
    <link rel="stylesheet" href="./index_files/print.css" media="print" type="text/css">

    <!--[if lt IE 9]>
    <script src="https://oss.maxcdn.com/html5shiv/3.7.3/html5shiv.min.js"></script>
    <![endif]-->

<!-- Begin Jekyll SEO tag v2.8.0 -->
<title>Molecular Dynamics Simulation</title>
<meta name="generator" content="Jekyll v3.9.2">
<meta property="og:title" content="Molecular Dynamics Simulation">
<meta property="og:locale" content="en_US">
<meta name="description" content="2D Molecular Dynamics Simulation">
<meta property="og:description" content="2D Molecular Dynamics Simulation using Leonard Jones">
<link rel="canonical" href="https://pm623.github.io/MolecularDynamicsSimulation/">
<meta property="og:url" content="https://pm623.github.io/MolecularDynamicsSimulation/">
<meta property="og:site_name" content="MolecularDynamicsSimulation">
<meta property="og:type" content="website">
<meta name="twitter:card" content="summary">
<meta property="twitter:title" content="Molecular Dynamics Simulation">
<script type="application/ld+json">
{"@context":"https://schema.org","@type":"WebSite","description":"2D Molecular Dynamics Simulation","headline":"Welcome to GitHub Pages","name":"MolecularDynamicsSimulation","url":"https://pm623.github.io/MolecularDynamicsSimulation/"}</script>
<!-- End Jekyll SEO tag -->


    <!-- start custom head snippets, customize with your own _includes/head-custom.html file -->

<!-- Setup Google Analytics -->



<!-- You can set your favicon here -->
<!-- link rel="shortcut icon" type="image/x-icon" href="/MolecularDynamicsSimulation/favicon.ico" -->

<!-- end custom head snippets -->

  </head>

  <body>
    <header>
      <div class="inner">
        <a href="https://pm623.github.io/MolecularDynamicsSimulation/">
          <h1>MD Simulation</h1>
        </a>
        <h2>2D Molecular Dynamics Simulation </h2>
        
          <a href="https://github.com/pm623/MolecularDynamicsSimulation" class="button"><small>View project on</small> GitHub</a>
        
        
      </div>
    </header>

    <div id="content-wrapper">
      <div class="inner clearfix">
        <section id="main-content">
          <h2 id="welcome-to-github-pages">Introduction</h2>

<p>An Altera System-on-Chip FPGA with an integrated ARM hard processor system (HPS) was used as the development platform to perform a 2D molecular dynamics simulation of symmetrical, inert atoms.
   Molecular interaction was visualized on a VGA display with molecules represented as circles interacting with each other and changing colors corresponding to the amount of energy each molecule had.
    The visualizer worked in tandem with a C program running on the HPS to allow for user input to control the simulation from the command line, as well as a USB mouse.
     Memory manipulation via the Qsys bus allowed for configurable communication between the HPS and the FPGA as data was passed to the FPGA from the ARM, and vice-versa.
      Correctness of the system was continuously verified throughout development by analyzing waveforms in ModelSim, using the onboard LEDs and visualizing the output on the VGA Monitor.
       The objective was to efficiently utilize the resources on the FPGA to be able to visualize molecular interaction on a reasonable timescale.
</p>


<h2 id="welcome-to-github-pages">Demo</h2>

<iframe width="560" height="315" src="https://www.youtube.com/embed/mpDcrlHr-kI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



<h2 id="markdown">Motivation</h2>

<p>Molecular dynamics is a notoriously computationally-expensive Newtonian modeling technique that has applications in everything from materials research to biochemistry. Given its complexity, acceleration of the simulation is an area that’s given significant research attention today. For our final project, we wanted to implement a 2D molecular dynamics simulation of a liquid that exhibits a phase change to a gas when a user adds energy to a system and vice-versa, in order to explore the viability of molecular dynamics simulations on an FPGA. Specifically, we want to observe phase changes when energy is added or removed from the system in the form of condensation/evaporation or crystallization/melting. Even though accurate molecular simulations require consideration of all 3-dimensions, we believe that creating a 2D version of the simulation can inform further efforts at accelerating the full 3-dimensional simulations.
</p>

<h2 id="markdown">Mathematical and Physical Background</h2>

<p>The simulation is iterative and involves two distinct processes, force calculation, and motion integration. </p>

<h3 id="markdown">Leonard Jones Potential</h3>

<p>The primary force of interaction for our simulation was the Leonarrd Jones (LJ) potential. The LJ model is great at capturing the interaction behavior between inert molecules because it captures the essential components of atomic interactions. LJ is a superposition of attractive and repulsive forces that give rise to an equilibrium atomic distance where the potential between the two atoms is minimized. Leonard Jones potential is given by the following equation.<br><br>





The repulsive term, proportional to 1/r12, captures electron repulsion due to the Pauli exclusion principle, where electrons in neighboring atoms cannot be allowed to occupy the same energy state, unless they have opposite spins. Orbitals of an inert atom are usually full, which means that bringing the electrons of these atoms close together requires an extreme amount of energy and is overall an unfavorable process. This repulsion is represented by the spike in potential with decreasing atomic distance as shown in the plot of the LJ potential below.<br><br>
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
The attractive force, proportional to 1/r6, captures dispersive interactions between atoms as a result of fluctuating partial charges on the surface of the atom. This should not be confused with bonding, which we model as an entirely different force. These types of weak interactions are akin to Van Der Waals forces which are a type of weak attractive interaction. The superposition of the attractive and repulsive forces yields a potential well at an equilibrium distance rm between atoms where the potential is minimized. Given the repulsive force is proportional to a much larger exponential, it falls off much more quickly with atomic distance than does the attraction, but it blows up exponentially on extremely small scales. <br><br>
  
In the first equation, epsilon is the dispersion energy, and regulates the depth of the potential well. Sigma is the distance where the particle interaction becomes zero. Both parameters can be manipulated depending on the types of molecules being simulated. The LJ potential captures basic phase changes very well which is why it was an appealing target for this project. <br><br>
  
To actually use the Loenard Jones potential in a force calculation, a derivative needs to be taken in order to calculate.<br><br>
  
  
In order to increase computational efficiency, the LJ potential is never directly calculated, because only the resulting force is required. As such, only the above equation appears in the project implementation.<br><br>
In the project, when calculating the LJ force we not only had to update the acceleration of the particle under question, but apply the opposite force (and associated acceleration) in the opposite direction to the particle that it's being compared to, in order to obey Newton’s 3rd law of motion. The record keeping of particle characteristics like position, acceleration, and velocity is discussed later.<br>
</p>

<h3 id="markdown">Other Forces</h3>

<p>Other forces necessary in making the simulation look realistic were elastic boundary interactions and elastic bond forces. Boundaries were treated as a stiff spring. The boundaries were not the borders of the VGA but boundaries of a drawn in box - the reason for this is discussed in detail later. If particle position ever exceeded the boundaries of the box a spring force F=-kx was applied to the particle. The k parameter was made sufficiently large so as to simulate a bounce off a hard wall. Gravity was made toggleable by the user and was the simplest force to implement. It just included a constant downward acceleration in the y direction on all particles. Particle bonding was modeled as another spring force. If two particle positions were within a defined radius of each other a force F=kx and F=ky were added to the x and y accelerations of the particle under question. Again, equal forces in opposite directions were applied to the other particle to obey Newton’s 3rd law. The k of this force was smaller and in the opposite direction than the k of the boundary interaction, but made a significant difference when it came to particle attraction. The repulsive force captured by the LJ force was so significant that it balanced the attractive forces of LJ itself as well as the attractive bonding forces.
</p>

<h3 id="markdown">Verlet Algorithm  </h3>

<p>The Verlet algorithm is the numerical method we used to calculate updates to particle position based on the total force of each particle. The algorithm is generally used to integrate Newton’s equations of motion and is commonly used in calculating particle trajectories in molecular dynamics simulations. The acceleration on each particle is directly calculated using the positions of all the particles at any given time. After we have calculated the net acceleration on all of the particles due to the forces described above,  the following equations are used to calculate the updates to particle position and particle velocity.<br><br>








This makes it necessary to recalculate position, velocity, and acceleration for every particle on each timestep. The velocity equation above is approximated in our implementation to just use a(t) instead of using the average acceleration of the current and next timestep. We found that this approximation in our simulation made little difference in the visual accuracy of the physics. To reiterate, on every timestep the algorithm uses the current locations of the particles to calculate, using the physics described above, the total forces on each particle. Acceleration is exactly equal to force in this case because we just set particle mass to 1 with arbitrary units. These accelerations are used to calculate the next position and velocity of each particle on every timestep. This continues cyclically until the process is killed by the user. <br></p>


<h2 id="markdown">System Design</h2>
<p>The flow of information in this lab is as follows: user input is communicated to the ARM via a C program running on the ARM through a USB mouse and input at the command line to control program state. The ARM, a bus master for communication on the Cyclone V board, passes particle positions to the FPGA via PIO ports where the hardware corresponding to the acceleration calculation for the simulator has been stored. A single solver on the FPGA reads in a pair of particle positions and calculates the forces on each particle due to the other. These forces are sent back to the ARM via PIOs where the ARM collects them and updates particle positions accordingly using the verlet algorithm. Particles are displayed in the top left region of the VGA from the ARM.</p>


<h3 id="markdown">High Level Design Considerations</h3>

<p>By simulating in 2D instead of 3D, we would immediately reduce computational complexity by 1 degree of translational freedom, and at least two degrees of rotational freedom. Since we are choosing to model symmetric molecules with symmetric charge distributions this would reduce complexity by another 1 degree of rotational freedom. Therefore, we expect to be able to model 200-500 molecules without exceeding the resources on the FPGA while still being able to visualize them moving in real time.<br><br>

  The time complexity of some select forces like interaction with the border wall is O(n) so, with a limited number of molecules in simulation, these calculations could easily be run in parallel on the HPS. Calculating the Leonard Jones interaction has a time complexity closer to O(n2) and would benefit from acceleration on the FPGA. The forces being calculated in parallel would need to be summed on the FPGA to then calculate the motion of each particle. LJ has terms with 1/r6 and 1/r12 dependencies. It might make sense to avoid direct computation of 1/r and opt for a lookup table for computational efficiency, although it has been reported that direct computation has been found to be more efficient in terms of logic utilization.<br><br>
  
  Additionally, real atomic vibration is on the order of GHz. This introduces an interesting challenge with visualization on the VGA. There needs to be some augmentation of data produced by the motion calculation such that particle motion can be visualized. This can be handled by playing with the discrete time step used in the verlet calculation such that vibrations can be visualized.  We could never simulate anything close to real vibrational frequencies anyway (especially because the system is clocked in MHz). In this way, the vibrational frequencies would be naturally reduced by slow computation.<br><br>
  
  Another hugely important design consideration for this project was the numerical precision. We’ve mentioned before that some of the units are arbitrary. This is essentially to scale up numbers so that they can be represented with fewer decimal points. Particle mass for example is assumed to always be 1. This certainly isn’t in grams, but it’s scaled so that floating point notation on the FPGA isn’t necessary. In a similar fashion, particle position was measured in pixels. That's not to say they were treated as integers the whole time. Floating point values were allowed during intermediate calculations but cast back to ints as soon as anything needed to be drawn to the VGA. On the FPGA side, 10.17 fixed point notation was used. 27 bits total were used so that we could use the multipliers in the DSP blocks in our solvers. 17 bits afforded us about 5 decimal places in base 10 format which we determined from many simulations in modelsim would keep our error relative to the floating point version to less than ~5%. This, we decided, was enough to maintain good visual accuracy of the physics. This meant that for larger quantities like particle position we had only 9 bits (because of the sign bit). Our solution to this problem was to limit particle position to the top left quadrant of the VGA so that particle positions could never cause overflow in our hardware. We initially tried to use floating point notation but the added precision was not worth the reduction in multipliers per solver. With fixed point we used 8 multipliers per solver while floating point required triple that.<br><br>
  
  In a similar vein to the numerical precision considerations, we had to figure out how to implement division in fixed point notation. This was determined to be best implemented using a lookup table which allowed our solvers to stay combinational, but more on this later
  <br><br></p>

<h3 id="markdown">Overall System Design</h3>

<p>The flow of information in this project is as follows: user input is communicated to the ARM via a threaded C program running on the ARM through a USB mouse and command line inputs. The ARM, a bus master for communication on the Cyclone V board, passes that information to the FPGA via PIO ports where the hardware corresponding to the acceleration solvers has been stored. The solvers read in particle position pairs from the PIOs and calculate the forces those two particles exert on each other, passing them through separate PIOs back to the ARM, so the C program running on the ARM can use those accelerations to compute a step of the verlet algorithm. The C program calculates the new positions of all the particles and draws them to the VGA display.<br><br>
The number of particles on screen is controlled by clicking the mouse on the VGA inside a “container”. This container is really just a vector field of accelerations on the VGA that directs particles inside of it, essentially simulating a container to hold the particles. The ARM controls all things VGA, by drawing the box, the mouse cursor, and the particles when needed. Each particle pair is iterated over during every step of the computation. Particle color is mapped using a linear scale corresponding to particle speed. The communication of particle positions from the ARM to the FPGA occurs as a memory mapping via PIO ports instantiated in the Quartus II software. The C program executing on the ARM takes user input in the form of command line inputs to configure the program, and mouse inputs to increase the number of particles on screen and their spawn location. Toggling gravity, adding and removing energy from the system, and changing the box size are some of the user actions the program allows.<br><br>
Each solver has its own set of PIOs to communicate with. The reason for this is it would only serve to reduce computation speed. The forces on each particle pair can be calculated within a single cycle so we wanted PIO communication to be parallel in hardware to support whatever speed the ARM could write to the PIOs at. Using a control module to arbitrate particle-pairs to the solvers would introduce a non-negligible start delay for each solver. As such, the number of solvers is not parameterized but fixed at 10. On every cycle of the Verlet algorithm the ARM queries the 10 solvers and passes them particle-position pairs, then immediately reads out the total force on each particle. Updates to the VGA happen in a user configurable time frame in terms of how many steps the algorithm should take before it redraws the VGA one time. Increasing the number of steps per draw can help reduce the computational load of drawing to the VGA, but reduces particle movement resolution. The ARM keeps track of particle parameters like position, velocity, and acceleration necessary for the verlet updates.<br><br>
</p>

<h3 id="markdown">C Program design</h3>
<p>The primary design considerations of the system involved deciding which parts of the LJ equation to offload to the hardware. Computing the forces (i.e. acceleration) involved around 8 multiplications and a division operation, moving that to hardware had the potential to offer the biggest improvement. The net forces on the particles are caused by their interactions and elastic bonds sent via pio ports. With a maximum of 10 parallel solvers, the system computes the interactions of 1 particle with 10 particles at a time.<br><br>

As described in the Mathematical Background, the walls act as a spring with a spring constant to govern the stiffness of the wall. When a particle goes out of bounds, the walls exert the force in the other direction and bring the particle back into the bounds.<br><br>
  
This net acceleration is used to calculate the position and the velocity updates of  the particles. The C program then draws the updated particles onto the screen  Additionally, a separate function determines the color of the particles based on the current velocity of the  particle. C’s ability to easily manipulate multiple arrays easily, influenced the decision to only calculate the acceleration in hardware.<br><br>
  
To enhance the user experience, a mouse interface was added to add the particles into the system. The raw data received from the mouse contains the relative change since the last update. So, to maintain an accurate record of the position, the mouse is initialized to be at the center of the screen and the relative change is accumulated to calculate the current position. On a single mouse click, the program creates 10 new particles with randomly initialized positions and velocities. This UX also allows the user to dynamically change the size of the box and add/remove energy from the system. The removal/addition of energy is handled by subtracting/adding a factor to the current velocity. Additionally, the gravity can be toggled to ??. The user can also dynamically change the box size using key inputs and the particles out of bounds at that time will be pushed into the box automatically.<br><br>
</p>


<h3 id="markdown">Hardware design</h3>

<p>To implement the equation in Fig ?? to calculate the forces, would require a minimum of eight multipliers and one inversion to calculate 1/r^2, allowing for a maximum of parallel solvers. One method to reduce the number of multipliers is to reuse a single multiplier and use a state machine to control the inputs to and outputs from it. However, this method has the disadvantage of taking multiple cycles to calculate the value.<br><br>

On the other hand, a  purely combinational implementation can compute the acceleration instantly and the ARM does not need to wait for the iterator to finish computation. This allows for a significant increase in the speed and also reduces the complexity of the implementation. <br><br>
  
To implement the calculation of 1/r^2, we opted to use a lookup table with varying precision. The maximum distance between the particles for interaction to occur is 15 pixels , This means that the lookup table needs to support values ranging from 0-255. However, only the interactions where the particles are close to each other have a significant effect. So, the rom table is more precise (with 0.2 of increments) when the r^2 is between 0-50 and coarser (with increments of 2) beyond that. These optimizations lead to a table of ~300 entries. The lookup tables are single ported and each iterator is initialized with its own table to allow for fast calculations. To support a value of 255 and maintain a good precision, a fixed point of 10.17 is chosen.<br><br>
  
A consequence of the fixed point arithmetic and the approximation from the lookup table is that occasionally, the attractions and repulsion may experience a sharp spike when the particles come very close to each other (i.e. the particle positions overlap). This tremendous increase in acceleration causes a ripple effect when the particles interact with each other and destabilizes the entire system. To prevent such anomalies, the attraction is capped to a maximum of 1 and the repulsion is capped to a maximum of 3.<br><br>
</p>

<h2 id="markdown">Results</h2>
<p>Phase changes were successfully observed in the molecular simulation.<br><br>

  Without gravity, molecular interactions without manipulating the energy of the system were akin to that of a liquid floating in space. When gravity is turned on the molecules seem to form fit the container just as a liquid would. Figure X demonstrates the composition fitting the container.<br><br>



















  Figure X. Molecules fit container like a liquid when gravity is toggled<br><br>
  
  The interesting part about adding and removing energy from the system is that the changes were not permanent. When energy was added to the system, the atoms would fly apart as bonds broke and the LJ potentials were escaped. Atoms would fly around the screen at high velocity and collide into the walls and each other without reforming bonds or getting stuck together. To do this though the add energy command in the user interface would have to be “spammed”. In other words, we would have to continuously add energy to keep the molecules in the simulated gas phase. Something about the modeled physics implicitly damped particle motion so that after a short period of time without adding energy we would observe a phase change back to the liquid state. This change is shown in figure ___ below.<br><br>
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  Figure X. Progression of condensation without gravity for a system with > 100 particles<br><br>
  
  
  Initially energy was added by the user to start the system in the gas phase. Figure X. demonstrates the implicit cooling effect we observed in the simulation. When given time to equilibrate clusters of molecules started to form and then larger ones would form as smaller ones collided with them. The opposite was true in the solid phase. Cooling the composition down continuously would leave molecules in a rigid crystal structure. Due to the way we handled bonding, each atom would form a bond with 6 neighbors, and a 2D hexagonal structure was observed.<br><br>
  
  
  
  
  
  
  
  
  
  
  
  
  
  Figure X. 2D Hexagonal solid structure<br><br>
  
  Left to equilibrate, the LJ interactions would lead to melting back into the liquid phase as particules started to interact and move again. Even in the liquid phase though, an organized structure could be observed. This suggests that the equilibrium state was a hybrid between a liquid and a solid. A lolsquid if you will. We also allowed the user to control the size of the container that the particles were in. Making the box smaller had an effect similar to what would be observed with adiabatic compression. Initially, as the box became smaller, particle interactions resulting from the shifting sides formed something similar to a wave<br><br>
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  Figure X. Wave like motion due to the user manipulating the size of the box.<br><br>
  
  As the box became small enough to inhibit molecular motion, forces on the boundaries forced closer intermolecular interaction.<br><br>
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  It’s hard to tell from a static picture, but when visualized in real time its clear the atoms are vibrating at much higher frequencies. This means their average velocity is greater and hence the temperature of the system is higher. We did not explicitly monitor or regulate temperature here, but used molecular speeds as a proxy. This is consistent with the result of adiabatic compression of a substance. <br><br>
  

</p>

<h2 id="markdown">Future Work/ What We Would do Differently</h2>

<p>The original goal of the project was to simulate water freezing. We underestimated the numerical complexity of the project (discussed in the numerical considerations section about required precision, number format, etc.) which took a lot of time to master, and led to us aiming for a more attainable final goal. We were still able to observe lots of interesting physics, as well as phase changes, in the inert molecule simulation we produced. Now that the groundwork has been laid for molecular dynamics simulations on the FPGA, we wanted to offer our perspective in hindsight about what could be improved/ what direction the project should be taken in for any future groups looking to work on something similar.<br><br>

The reason we only performed the force/acceleration calculation on the FPGA was an issue of numerical precision, and limited FPGA resources. The position update in the verlet algorithm without a doubt required floating point precision due to the a(t)*t2 component. This meant that in order to avoid using a floating point unit on the FPGA (which required 3 DSP block multipliers per multiply), we had to run that calculation on the ARM. The consequence of this was that the ARM then also needed access to the position, velocity, and acceleration arrays, so it became the record keeper for the simulation. As a result, it also made sense to draw to the VGA from C since it had the record of all particle positions. As an aside, we were never concerned about drawing to the VGA because we were only utilizing a small fraction of the VGA at any given time. Doing the verlet update in C is okay because it has O(n) time complexity, but it introduced a bottleneck in communicating particle pairs to the solvers because we still needed to complete as many transactions over PIOs as there were particle pairs because the FPGA did not have its own record of particle positions, nor did with think it was a good idea to maintain separate lists that we would have to synchronize. Our rate limiter then seemed to be the PIO transactions. <br><br>
  
One thing we could have done differently is by allocating a block of shared SRAM that the ARM could write all particle positions to, and then another block that the solver would write the updated accelerations to. On the ARM side, this would mean that instead of writing all particle pairs to PIOs, it would only have to do writes and reads with O(n) time complexity. On the FPGA side though, the solvers would have to become sequential whereas in their current form they are combinational which we saw as a benefit to keeping the PIOs. The FPGA calculation would then have O(n2) time complexity anyways because pairs would still need to be read from the SRAM/M10k shared memory. It was not clear to us that this method was going to be faster than the first, especially considering the ARM is clocked at a much higher frequency. <br><br>
  
It seems then, that in order to truly accelerate the calculation on the FPGA everything must be done on the FPGA. The force calculation, the record keeping, the verlet update (in floating point), and drawing to the VGA. One potential problem in doing this is that we used lookup tables to calculate 1/r which had heavy logic utilization, so a logic resource constraint on the FPGA must be carefully avoided. It is a serious challenge, but one that could be doable with lots of careful planning.<br><br></p>


<h2 id="markdown">Conclusion</h2>


<p>A Leonard Jones molecular dynamics simulation was implemented on an Altera System-on-Chip development platform. The simulation successfully demonstrated the interaction of hundreds of inert molecules on a reasonable timescale such that atomic vibrations could be visualized in real time. The simulation also demonstrated phenomena associated with inert molecules like phase changes and adiabatic effects during compression. Though we did not get all the way to simulating water, requiring a non-uniform potential distribution, the simulation still provides good insight into molecular dynamics and its viability on an FPGA. The project serves as a good basis for continued work, especially in the direction of acceleration (supporting more particles) or physical complexity (non-uniform potential distributions for asymmetric molecules).<br><br></p>


<h2 id="markdown">Everyting below is junk</h2>

<p>Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for</p>

<div class="language-markdown highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Syntax highlighted code block

<span class="gh"># Header 1</span>
<span class="gu">## Header 2</span>
<span class="gu">### Header 3</span>
<span class="p">
-</span> Bulleted
<span class="p">-</span> List
<span class="p">
1.</span> Numbered
<span class="p">2.</span> List

<span class="gs">**Bold**</span> and _Italic_ and <span class="sb">`Code`</span> text

<span class="p">[</span><span class="nv">Link</span><span class="p">](</span><span class="sx">url</span><span class="p">)</span> and !<span class="p">[</span><span class="nv">Image</span><span class="p">](</span><span class="sx">src</span><span class="p">)</span>
</code></pre></div></div>

<p>For more details see <a href="https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax">Basic writing and formatting syntax</a>.</p>

<h3 id="jekyll-themes">Jekyll Themes</h3>

<p>Your Pages site will use the layout and styles from the Jekyll theme you have selected in your <a href="https://github.com/pm623/MolecularDynamicsSimulation/settings/pages">repository settings</a>. The name of this theme is saved in the Jekyll <code class="language-plaintext highlighter-rouge">_config.yml</code> configuration file.</p>

<h3 id="support-or-contact">Support or Contact</h3>

<p>Having trouble with Pages? Check out our <a href="https://docs.github.com/categories/github-pages-basics/">documentation</a> or <a href="https://support.github.com/contact">contact support</a> and we’ll help you sort it out.</p>

        </section>

        <aside id="sidebar">
          

          
            <p class="repo-owner"><a href="https://github.com/pm623/MolecularDynamicsSimulation">MolecularDynamicsSimulation</a> is maintained by <a href="https://github.com/pm623">pm623</a>.</p>
          

          <p>This page was generated by <a href="https://pages.github.com/">GitHub Pages</a>.</p>
        </aside>
      </div>
    </div>

  

</body></html>