---
title: "Sapper: Acerola Jam 0 Entry"
draft: true
tags:
  - ComputerScience
  - Godot
  - GameDevelopment
---
# It Begins!
Acerola Jam 0 ran from February 28th to March 14th. The (most important of the) rules: all submissions must be done solo and no AI. The theme: aberration. 

My idea for this jam was to make a 3D, first person minesweeper, where the player needed to use a metal detector in order to find mines in a given area. This related to the theme because an aberration is, according to the jam page itself: "a departure from what is normal, usual, or expected, typically one that is unwelcome", which I feel suits getting your legs blown off by a mine quite well.

I chose Godot 4.2, of course, as the engine as I know how to use it and it is pretty fast. I planned to include the following features:
- WASD movement with mouse look (no jumping!)
- mines that would kill player if walked on
- metal detector that would light up if a mine was under it
- ability to flag a mine
- van used to exit level after player is confident that all mines have been flagged
- grading system used to evaluate how well the player did
- retying a level after death

I decided to tackle these features in that order in order to make sure that I always had the most playable version of my game at any given time. This would give me time to adapt if a feature didn't work, or if my game design was too boring.

A lot of these features are simple enough to just figure out so I don't feel the need to talk  too much about them, however there were some interesting things that I found while making this game that I would like to talk about.
# Godot Silliness
To try and make my game look nice, I used a few graphics tricks. The first of these was (supposed to be) and fog shader. This should be fairly simple, just make a quad cover the camera, add a pixel shader to said quad, and make a very simple depth fog using the depth buffer. Basically just this: https://www.youtube.com/watch?v=EFt_lLVDeRo. For some reason though, Godot removes transparencies the screen texture, which meant that any transparent or translucent objects would simply not be drawn with custom post processing. So I had to turn off my transparent objects to make it work. Fun.

Terrain. Godot has no built in system to handle this, so either you have to roll your own or use a plugin. I had 14 days to make a full game, so I went with a plugin. I used the pretty nice Terrain3D plugin and it worked fairly well. Until I wanted to put things on the terrain.

Godot also lacks a built in foliage system, again either roll your own or use a plugin. I attempted to use ProtonScatter for this, but it was painful not being able to paint on foliage and it just didn't feel like working sometimes. So I moved on to Spacial Gardener, which worked much nicer... except that LODs and culling seemed to be busted. Too bad for anybody who needed to use it on large levels. Both plugins did not like Terrain3D very much though; in order for foliage to actually be placed along the terrain, debug collision view had to be enabled, which slowed down my tiny little laptop I was using. So much weirdness, so little time. If I were to make a game without the 14 limit, I would for sure just write my own terrain and foliage system that actually worked well.

Blender integration seems great on the surface, but can actually get very annoying very fast. This is because .blend files are imported as scenes and not as mesh resources. This means that you cannot edit their properties in the Godot editor and makes adding shaders to materials annoying. While it is possible to work around this, I found it easier to just export the models as an OBJ and import it that way. Maybe I'm doing it wrong, but it just feels weird.

# Godot Un-Silliness
Godot is really easy to program with. It's script system makes it very easy to add functionality to nodes and it's very simple node system makes it very simple to understand what nodes have what you need. Coming from Unreal Engine, this was quite nice as Unreal Engine has very deep inheritance and does not use scripts that are "attached" to nodes. Combined with fairly nice documentation and a very simple user interface, Godot stays out of your way very well. All this to say that Godot is great for making just about everything yourself while Unreal is better for just having built in functionality.

Godot is also incredibly lightweight as an editor, taking up very little storage space, loading very quickly, compiling very quickly, and overall running well. 

# What I Learned During This Jam
In previous projects, I had found the power of signals and started to truly appreciate them. Here, I think I appreciated them too much. My code required me to manually connect signals between the player and each mine present in the scene, which was very time consuming with levels having somewhere around 4-15 mines each. Having a system that scans the scene tree and automatically connects signals is something that I will definitely use next time I make a game in Godot as it will save me a lot of time.

Plugins are nice, but do not rely on them too much. Trying to bend other peoples' work to fit what I want may be harder than just trying to make something yourself and I learned that through struggling to make terrain and foliage work. In the future, I will try to make my own systems rather than using plugins to take care of things that I think may be too hard.

Raytracing is the future. Maybe not yet, maybe not next year, but one day (hopefully soon) raytracing will be the new normal for global illumination and reflections and game developers everywhere will be free from carefully placing reflection probes and fixing broken lightmaps. And since Godot is open source, that day will be as soon as I figure out how to do that.

# What To Learn Next
I still have not made a game that allows the player to save their game. I know enough to be able to do this, I just haven't because it seems very tedious and like it would take a long time. Making a game with this feature is definitely something I should explore in the future.

Since a lot of the issues I am having have to do with graphics, I should probably learn how to fix them myself. Writing graphics stuff seems like a nice goal, but not very specific. When I come up with a "graphics thing" that seems like a good exercise for me to try, I will be sure to do that.