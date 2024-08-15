---
title: Real Time Foliage Wind Animation Using The Fast Fourier Transform
draft: false
tags:
  - GraphicsProgramming
  - Godot
  - ComputerScience
---
By Ruby-Dragon

Source code: https://github.com/Ruby-Dragon/FFTGrassAnimation
# Introduction

$\quad$Foliage is quite a common sight in video games. Noteworthy games to employ dense foliage throughout the past couple of decades include *The Legend of Zelda: Breath Of The Wild* (2017), *The Witcher 3: Wild Hunt* (2015), and *Crysis* (2007). Large forests and fields depicted in these titles benefit greatly from the well made 3D models are textures used. Unfortunately, these high quality assets can be let down by the imprecise animation methods used.

 $\quad$ Foliage Animations are noticeably repetitive in games today, feeling unnatural and degrading the contribution of the foliage to the visuals of the scene. While playing through games such as those mentioned earlier, I noticed the strangeness of the foliage animation. This repetition took the form of both foliage moving identically in different locations on screen and of individual foliage objects repeating the same motions infinitely with little or no variation. Once I noticed this flaw, I could not help but see this unnatural look throughout many of the games that I played, which negatively impacted my perception of the overall graphics quality. Solving this issue seemed like a good way to improve the graphics quality of video games. My goal for this paper was to determine a better method for animating foliage to avoid that unnatural repetitiveness. I decided to start by determining a better method for animating grass, as the physics involved in the motion are fairly simple - at least compared to more complex objects such as trees or bushes.

# Literature Review

$\quad$Before I can develop a method for animating grass, I must first choose create a test environment, and that means picking a method for animating grass. Historically, grass was rendered using a technique known as billboarding. This method is described in Chapter 1 of GPU Gems, a compilation of computer graphics techniques written by various industry professionals. Kurt Pelzner explains how in this method, multiple flat planes are arranged in a star pattern with a grass texture projected onto each plane in order to give the illusion of more geometric detail where there is really none (Pelzner, 2004, pp.110-111). It was also heavily used in video games, with games such as *Minecraft* and *The Elder Scrolls 5: Skyrim* being prime examples. Despite this popularity, the billboarding method has a relatively large drawback in that it has poor visual quality. This visual quality difference is particularly noticeable when viewed from above, as the star pattern becomes easily discernible. This is less than ideal for use in an application such as a video game where players can position the camera to view the grass from various angles, including from above. While this method worked well for running on computers with less computing power, modern computers have improved performance greatly, so a more sophisticated method should be employed instead.

$\quad$Recently, a new method has been increasing in popularity, in which GPU instanced geometry is used to render grass blades as individual geometry. As the method has only begun to grow in popularity in the industry quite recently, many works detailing the method are produced by industry professionals rather than scholarly literature. In a video posted to YouTube, Associate Graphics Programmer at Sony Entertainment, Garett Gunell, describes how he used GPU Instancing to scatter a simple 3D model of a blade of grass hundreds of thousands of times across a plane in order to create a field of grass with reasonable performance on his, relatively weak, personal computer (Gunell, 2021a). This GPU Instancing method he used is where the data of a 3D model is stored on the GPU and is copied to defined locations in space instead of each instance of the model requiring the CPU to process it first. By skipping this extra processing step, Gunnel is able to maintain a high frame rate of 500 FPS despite the 360,000 blades of grass being rendered (Gunell, 2021b). Sucker Punch Productions also used this method to create fields of grass with real geometry for their game Ghost of Tsushima as described by Eric Wohllaib, a developer at Sucker Punch Productions, with spectacular results (Wohllaib, 2021, slide 14). The amount of geometric detail they were able to capture allows for visuals that continue to look good under close scrutiny, and shows that using GPU Instancing with fully modeled grass blades is the best method for rendering grass now, and is a good choice to test animations on.

$\quad$Unlike the methods of rendering grass, the methods of animating grass have been largely unchanged in much of the industry for a long time, typically creating simple low quality motion. In the same chapter of GPU Gems where the billboarding method is explained, Pelzner describes “a calculation based on trigonometric functions, especially sine and cosine” for animating the billboarded grass (Pelzner, 2004, p. 112). He then leaves it up to the reader to implement this however they feel is best. While vague, it can be inferred that Pelzner used the oscillating nature of sine and cosine functions to induce a periodic swaying motion in the grass. This appears to be the same method used in the game *Baldur’s Gate 3* (2023) by Larian Studios, in which foliage can be seen swaying back and forth in perfectly periodic motions. Clearly, not much change has been made over the nearly 20 years between the method’s description in GPU Gems and its use in games. Other implementations of foliage animation are similar in terms of the complexity of motion they create. The method employed in the game Crysis - by Crytek and released in 2007 - as described by Tiago Sousa in GPU Gems 3 relies on “triangle waves'', which are a “cheaper” way of simulating trigonometric functions such as sine or cosine  (Sousa, 2007, p. 247). These simple methods sacrifice quality as they are only creating a loose visual approximation of what wind looks like, instead of basing their methods on an empirical model of wind behavior that relies on data from real world measurements.

$\quad$Empirical models of wind behavior are, however, being used to animate ocean waves in computer graphics, as wind is the driving force behind large scale ocean waves. One common, and still simplified, method of doing this is to use a series of functions called Gerstner waves. According to Jerry Tessendorf, these are “an approximate solution to the fluid dynamic equations”  (Tessendorf, 2001, p. 7). This method is bundled by default in Unreal Engine for use in simulating waves on the surface of bodies of water (Epic Games, 2024), which supports the idea that these waves model wind behavior well. However, Tessendorf points out that “oceanographic literature tends to downplay Gerstner waves as a realistic model of the ocean” and argues that a better method should be used instead  (Tessendorf, 2001, p. 8). This better method is known as the Fast Fourier Transform (FFT) method. As explained in Tessendorf’s paper, this method generates a heightmap using the Fast Fourier Transform, which can then be applied to a subdivided plane to create the surface of the ocean  (Tessendorf, 2001, p. 10). Tessendorf argues that by using “statistical models… in combination with experimental observations”, his method creates a more realistic ocean simulation (Tessendorf, 2001, p. 8). The developers of the game *Atlas*, Grapeshot Games, agree with this sentiment, as they used the method to create the ocean surface of the game. In addition to its benefits to realism, Mark Mehelich, one of the developers, expressed in his Game Developers Conference (GDC) presentation that the FFT method was “nice and easy to use” and that it could “cover infinite areas seamlessly”  (Mihelich & Tcheblokov, 2019, slide 9). The FFT method appears to be one of the best methods for simulating ocean wind waves in real-time due to its high quality and ease of use, as attested to by game developers who are actively using this method. 

$\quad$While in the past, the FFT method was considered too computationally expensive to be used in real-time applications, modern computer hardware is far more advanced than the hardware available when the method was first developed. Tessendorf developed his method using a “1+ GHz cpu” where “512x512 FFTs can be generated at nearly interactive rates” (Tessendorf, 2001, p. 10). What he means by a 512x512 FFT is that 512 FFTs were calculated for both the x and y-axis of the heightmap, meaning 262,144 FFTs in total, where more FFTs calculated leads to increased quality. That level of performance, “nearly enough” is still not enough for use in a full video game that also has other effects that need to be rendered, turning nearly enough into not enough. In the modern day, however, implementations can take advantage of advancements such as GPGPU programming, where a GPU is used for General-purpose Programming instead of a CPU, which can lead to a massive increase in the performance of the FFT method. To illustrate the difference in possible performance, Fynn-Jorin Flügge at the Hamburg University of Technology explained that “while the Intel Ivy Bridge CPU architecture reaches around 600 GFLOPS… NVIDIA’s Geforce GTX 1080 Ti GPU reaches a performance of 11 TFLOPS” (Flügge, 2017, p. 6). By using the large amount of computing power found in a GPU by using GPGPU programming, the performance of the FFT can be increased dramatically. 

# Methodology

$\quad$To test if the FFT method could be viable, I implemented it to animate a field of GPU Instanced grass blades in the Godot Game Engine. I chose Godot simply because I have previous experience in it. I also implemented the trigonometric method in order to compare the performance of both methods. To start, I created a new Godot version 4.2 3D scene using the default Forward+ renderer.

## Creating A Field Of Grass

$\quad$Using GPU Instancing on individual models of grass blades will allow me to see the results of the FFT method on a high-quality model that is similar to the methods that modern games use to render grass. The 3D model for the blades of grass I used is a simple 3D model I created using Blender with 7 vertices, as shown in [[#Figure 1 | Figure 1]] The model is later given a simple gradient color: dark at the base to light at the tip. This paper focuses on the animation of the grass, not the shading and texturing, which is why a simple gradient was used rather than more advanced techniques.

### Figure 1
![[grass_screenshot.png]]

$\quad$Since I am using the Godot Game Engine, I made use of the built-in features of the engine to create the grass field. Godot has a built-in system for GPU Instancing called MultiMesh3D, which can “draw up to millions of objects in one go… use\[ing\] the GPU hardware” (Godot, 2024), meaning that a large amount of grass blades can be drawn at once, instead of individually, which improves performance. For me to fully evaluate the impact of the FFT method on the performance of the scene, I need to ensure that the scene runs with a high level of performance to start with.

$\quad$After adding a MultiMeshInstance3D to the scene, I used the built-in Populate Surface tool to place 65,536 instances of the grass blade mesh across a 30 meter by 30 meter plane, creating a field of grass with a density of about 72.8 blades per square meter as shown in [[#Figure 2 | Figure 2]] The rotation, location, and scale of each blade was offset by a small random amount in order to make the field look more realistic as grass does not grow evenly in the same direction. I then created a vertex shader, which will be explained in the next section, attached to the grass mesh where I programmed the animation.

### Figure 2
![[grass_field_screenshot_2.png]]
*Note: The white box is just added for scale, it is approximately the height of a human*

## Generating Wind Using The FFT

$\quad$The FFT method described by Tessendorf has already been implemented in a Godot 4.2 plugin by the GitHub user Tessa Avery. This plugin generates the ocean heightmap via FFT in a compute shader (Avery, 2022) and allows for the heightmap to be sampled in custom code. I made use of this feature to generate the heightmap, and then use it as the map of wind strength over the field. I tested a variety of FFT resolutions and found that 128x128 was the lowest I could set the resolution before the quality loss because noticeable, and so I used 128x128 resolution as it was the best balance of performance to quality. The compute shader should greatly improve performance over the CPU-based approach used by Tessendorf. There is a slight limitation to this, however, which will be discussed in the limitations section of this paper.

$\quad$I used a vertex shader to apply the movement to the grass blades. In order to make the grass model bend instead of translate through space, the movement of the vertex must be proportional to the height of the vertex from the base of the mesh. To do this, I multiplied the wind strength that would be applied to the vertex by the height of the vertex squared, creating a simple quadratic curve using the vertex’s y coordinate, to find the amount of displacement the vertex will be offset by. While a very simplistic model compared to more advanced models such as the quadratic bezier curves used by Jahrmann, the complexity created by implementing a more accurate physics model would extend out of the scope of this paper.

$\quad$To bend the grass in the direction that the wind is moving in, I calculated a direction vector based on the user-configurable wind angle used in the FFT plugin. By multiplying the amount of displacement of the vertex calculated earlier by this direction vector, the final offset that can be applied to the vertex is found. A formula representing the final offset of the vertex using all of the operations discussed above is as follows:
$$
\mathbf{f}(x,y,z) = y^2 \times h(x,z) \times a \times \mathbf{d}
$$
where *y* is the height of the vertex, *h* is the FFT plugin value at given *x* and *z* coordinates along the horizontal plane, **d** is the wind direction unit vector, and *a* is an artistic amplitude control that controls how much the grass is moved by the wind. A large *a* value would result in a large amount of displacement while a smaller value would result in less displacement. I used an *a* value of 0.1 in my scene, which gave fairly nice-looking visual results. Using this formula as the offset for each vertex, I created an animated grass field using the FFT method.

$\quad$I also implemented a simple trigonometric function based animation in order to compare the performance of both methods. This method was a very simple sine-based calculation. I reused the quadratic curve idea from the FFT method and created this second formula to represent the offset of the vertex:
$$
\mathbf{f}(x,y,z) = y^2 \times (sin((x \times \mathbf{d}_x + z \times \mathbf{d}_y + t) \times a) +1) \times b
$$
where *x* and *z* are the coordinates of the vertex along the horizontal plane, **d** is the wind direction unit vector, *a* is the speed of the wind, *b* is an amplitude control that corresponds to the *a* value of the FFT method, and *t* is time. I used the same value of 0.1 for *b* as in the FFT method. This formula was also implemented in the vertex shader as well, allowing me to easily switch between the two methods by disabling the offset calculation of one of the methods. Using this method, the same field of grass is animated using the trigonometric method instead of the FFT method.

## Gathering Performance Data

$\quad$Godot has a built-in function for retrieving the current frame rate of the engine, which is the number of frames being rendered per second, measured in FPS. In real time applications, more FPS is better as it means that motion appears smoother. By logging the FPS of the test scene in a file, I could access the frame rate of the engine over any given period of time. To find the performance of both methods, I logged the current frame rate every 1/60th of a second for 60 seconds and then averaged the results for both methods. When gathering performance data on the FFT method, the trigonometric method was fully disabled, and when collecting data on the trigonometric method, the FFT method was also fully disabled, with the compute shader turned off. The I used was a Lenovo Legion 5 (2021) laptop with a Ryzen 7 5800h CPU, 16GB of RAM, and an NVIDIA GeForce RTX 3060 (6GB) GPU. 

# Results

$\quad$The average frame rate over 60 seconds for the FFT method was 176 FPS. The average frame rate for the trigonometric method over the same period was 203 FPS. Based on these numbers, it is clear that the FFT method performs noticeably worse than the trigonometric method in terms of frame rate as it is slower by 14.25\%. The method is still fast enough on modern hardware for real-time use though as 176FPS is clearly real-time. Though the method may be more expensive in terms of performance, the FFT method does have far superior visual quality compared to the trigonometric method.

### Figure 3
![[FFTGrassScreenshot.png]]
### Figure 4
![[TrigGrassScreenshot.png]]

$\quad$By comparing [[#Figure 3 | Figure 3]] and [[#Figure 4 | Figure 4]], it is clear that wind is modeled in much more detail in the FFT method ([[#Figure 3 | Figure 3]]) compared to the trigonometric method ([[#Figure 4 | Figure 4]]). Different locations in the field have different gusts of wind blowing on them, creating a wide variety of motion in the grass in the FFT method. This is in comparison to the trigonometric method which creates a distinct line of motion through the grass with little visual variety. While unable to be fully described in writing or still images in this paper, the FFT method looks far superior in motion, with pockets of local wind pushing the grass in interesting and natural-looking ways. In comparison, the trigonometric method is incredibly basic, and only shows a single gust of wind moving in one direction through the grass repeatedly, which does not look particularly natural. The FFT method is the clear winner in terms of visual results due to its increased visual complexity and more natural look.

# Conclusion
$\quad$This paper explored the use of the Fast Fourier Transform for simulating wind effects on foliage for real-time applications and found that it appears to be a viable method for increasing the graphical fidelity of games today. By reinterpreting the heightmap generated by the FFT method as used to animate ocean waves, highly detailed foliage animations can be generated in real time. While the FFT method does incur a performance drop as compared to the trigonometric method, the difference is not particularly problematic on modern hardware which can render with high enough performance using either method.

$\quad$This method can be applied to video games moving forward. The ease at which plugins implementing the FFT method such as the one used in this study, can be added into game engines greatly helps simplify the process of incorporating the FFT method into video games. This paper may also serve well as a guide for developers keen on using the method in their own projects as the implementation steps are clearly given in the methodology. In addition, developers that already plan on using the FFT method for ocean animation could adapt their pre-existing system to animate foliage. The same heightmap could even be used for both animations, negating the FFT method’s performance impacts for use in foliage as no extra heavy computation would be required at that point. The ease of use of this method only further solidifies its validity for use in modern games.

$\quad$While the FFT may be efficient enough for many modern computers, some users may desire the extra performance lost by the method or may not have a powerful enough computer. Therefore, the user should ideally be able to disable the FFT method in favor of a computationally simpler method such as the trigonometric method through a video game’s settings menu or the like.

# Limitations
$\quad$A key limitation of this paper is found in the choice of game engine used. In Godot, the buffer of data created by a compute shader (the heightmap in this case) can’t be read by a different shader on the GPU (the animation vertex shader in this case). Instead, the buffer must be synchronized to the CPU and then transferred back to the GPU in a new buffer before the vertex shader can access it. This process means unnecessary processing steps that likely impacted the performance of the FFT method in my testing. This limitation appears to be distinct to the Godot Game Engine as when Wohllaib describes the render pipeline used to render the grass for *Ghost of Tsushima*, he shows that the data created by their compute shaders is read back directly by their vertex shaders without this extra synchronization step (Wohllaib, 2021, slide 22). If a developer uses the FFT method in another engine, such as the one used for *Ghost of Tsushima*, the performance could theoretically be higher compared to the trigonometric method than the results I found as a result of my methodology.

$\quad$While the FFT method produced good visual results with the default FFT simulation settings, it is not known whether good visual results can be produced when attempting to recreate alternate weather conditions. For example, this paper tested a calm, slightly breezy wind, but game developers may wish to create stormy weather with high winds. As I have not attempted to create those conditions in this paper, it is possible that another method for animating foliage may produce better results than the FFT method when simulating other weather conditions.

$\quad$This study was also limited to comparing the FFT method and the trigonometric method. Newer methods are being explored by game developers as well, with *Ghost of Tsushima* using Perlin noise to drive their grass animations (Wohllaib, 2021, slide 31). A comparison between the FFT method, the trigonometric method, the Perlin noise method, and any other methods that may have been developed would be useful to determine which method is the most effective. This study mainly focused on whether the FFT method was feasible for real time rendering, while a different study could determine if the method is actually ideal for real world use. A study comparing multiple methods could also address previous limitations, such as alternative weather conditions and alternative game engines such as Unity or Unreal which may not be limited the same way that Godot is. A future study comparing multiple methods in multiple weather conditions in a different engine would provide a deeper understanding into the effectiveness of the FFT method and its alternatives and therefore should be conducted in the near future.

# Bibliography
<p style="padding-left: 2em; text-indent: -2em;">
Avery, T. (2022). Godot4-oceanfft. <a href="https://github.com/tessarakkt/godot4-oceanfft">https://github.com/tessarakkt/godot4-oceanfft</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
	  Epic Games. (2024). Simulating waves using the water waves asset. https:
//docs.unrealengine.com/4.27/en-US/BuildingWorlds/Water/WaterWaveAsset/
Flügge, F.-J. (2017). Realtime gpgpu fft ocean water simulation.
<a href="https://tore.tuhh.de/dspace-cris-server/api/core/bitstreams/8dc34c7a-f9f1-4d34-
bebe-d397ac76b9f4/content">https://tore.tuhh.de/dspace-cris-server/api/core/bitstreams/8dc34c7a-f9f1-4d34-
bebe-d397ac76b9f4/content</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Godot. (2024). Optimization using multimeshes.
<a href="https://docs.godotengine.org/en/4.2/tutorials/performance/using_multimesh.html">https://docs.godotengine.org/en/4.2/tutorials/performance/using_multimesh.html</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Gunnel, G. (2021a). How do games render so much grass? Youtube.
<a href="https://www.youtube.com/watch?v=Y0Ko0kvwfgA">https://www.youtube.com/watch?v=Y0Ko0kvwfgA</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Gunnel, G. (2021b). Modern foliage rendering. Youtube.
<a href="https://www.youtube.com/watch?v=mpbWQbkl8">https://www.youtube.com/watch?v=mpbWQbkl8</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Jahrmann, K., & Wimmer, M. (2017). Responsive real-time grass rendering for general 3d
scenes. Proceedings of the 21st ACM SIGGRAPH Symposium on Interactive 3D
Graphics and Games. <a href="https://doi.org/10.1145/3023368.3023380">https://doi.org/10.1145/3023368.3023380</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Orthmann, J., Salama, C., & Kolb, A. (2009). Gpu-based responsive grass.
<a href="https://www.cg.informatik.uni-siegen.de/sites/www.cg.informatik.uni-
siegen.de/files/data/Publications/2009/orthmann09grass.pdf">https://www.cg.informatik.uni-siegen.de/sites/www.cg.informatik.uni-
siegen.de/files/data/Publications/2009/orthmann09grass.pdf</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Pelzner, K. (2004). Rendering countless blades of waving grass. In Gpu gems: Programming
techniques, tips and tricks for real-time graphics (pp. 107–121). Pearson Higher
Education.
</p>
<p style="padding-left: 2em; text-indent: -2em;">
FFT FOLIAGE WIND ANIMATION 17
Sousa, T. (2007). Vegetation procedural animation and shading in ’Crysis’. In Gpu gems 3
(pp. 246–255). Addison-Wesley Professional.
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Tessendorf, J. (2001). Simulating ocean water. SIG-GRAPH’99 Course Note. <a href="https://
people.computing.clemson.edu/~jtessen/reports/papers_files/coursenotes2004.pdf">https://
people.computing.clemson.edu/~jtessen/reports/papers_files/coursenotes2004.pdf</a>
</p>

<p style="padding-left: 2em; text-indent: -2em;">
Wohllaib, E. (2021). Advanced graphics summit: Procedural grass in ’Ghost of Tsushima’.
<a href="https://www.gdcvault.com/play/1027033/Advanced-Graphics-Summit-Procedural-
Grass">https://www.gdcvault.com/play/1027033/Advanced-Graphics-Summit-Procedural-
Grass</a>
</p>