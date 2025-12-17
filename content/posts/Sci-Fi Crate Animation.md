---
author: Alex Figar
title: Sci-Fi Crate Animation
date: 2025-08-10
lastmod: 2025-08-10
tags:
  - Blender
  - 3D
categories:
  - 3D Art
Draft: "0"
Cover:
  image: /lootbox/render.jpg
  responsiveImages: false
  relative: false
ShowToc: true
---
For an assignment I was tasked with creating a loot box animation for a Sci-Fi game. The assignment required me to create 1x loot box and 1x piece of rare loot and document the creative process through concept art, story boarding and blocking so they could be sure we didn't use AI.
<!--more-->
## Concept Art

 For the crate I really just added low angular lines to a cube  to achieve the "Sci-Fi" look and went from there. Clustered most details around the top where the box opens but still added some bevels and chamfers to the bottom.

There was a lava lamp in the room I was working in and I thought it kind of looked like an energy cell sorta thing... So now the rare loot is an energy cell looking thing with a particle effect in the centre.

| ![](/lootbox/conceptartbox.jpg) | ![](/lootbox/conceptartitem2.jpg) |
| ------------------------------- | --------------------------------- |

## Grey Box
Bringing the 2D drawings to life in 3D. I accidentally made the rare loot absolutely huge but I'm just going to scale it down to whatever fits in the crate later. 

| ![](/lootbox/GreyBoxOpen.png) | ![](/lootbox/GreyBoxLoot.png) |
| ----------------------------- | ----------------------------- |
## Wire frame

I added the locking mechanism on to the notch of the loot box and forwent the energy particles in favour of a miniature blackhole, but without its accretion disk it looks pretty bad.

| ![](/lootbox/LootBoxWireframe.png) | ![](/lootbox/RareLootWireframe.png) |
| ---------------------------------- | ----------------------------------- |

## Better Blackhole Shader

In an attempt to remedy the lacking black hole effect I created a new black hole material and an accretion disk.
This is unfortunately way too expensive to render in an animation using cycles so I need to make a cheaper one.

![](/lootbox/blackhole.jpg)

## First Renders

The accretion disk is now a separate plane with a texture applied to it so it doesn't take forever to render this animation and the rare loot has a much nicer brushed copper material.

| ![](/lootbox/FirstRenderBox.png) | ![](/lootbox/FirstRenderLoot.png) |
| -------------------------------- | --------------------------------- |

## Opening Animation

My first attempt at the opening animation. Its looking a little lifeless, even with the little bounce at the end.
<video width= 100% src="/lootbox/OpeningAnimation.mp4" controls> </video>

## Baking Textures

Making sure that the texel density is even so that when baking no areas are blurry/low res.

| ![](/lootbox/BoxTexel.png) | ![](/lootbox/LootTexel.png) |
| -------------------------- | --------------------------- |

After unwrapping the models and making sure their texel density was ok, I baked out various maps to make rendering the animation faster.

| ![](/lootbox/BoxUV.png) | ![](/lootbox/LootUV.png) |
| ----------------------- | ------------------------ |


|           |                                |                                 |
| --------- | ------------------------------ | ------------------------------- |
| Diffuse   | ![](/lootbox/BoxDiffuse.png)   | ![](/lootbox/LootDiffuse.png)   |
| Normal    | ![](/lootbox/BoxNormal.png)    | ![](/lootbox/LootNormal.png)    |
| Metalic   | ![](/lootbox/BoxMetal.png)     | ![](/lootbox/LootMetal.png)     |
| Roughness | ![](/lootbox/BoxRoughness.png) | ![](/lootbox/LootRoughness.png) |

If these models were assets for a game I would spend a lot longer on the UV islands to make sure that every pixel counts, But because this is just an animation I'm dialling up the texture size to 8k and calling it a day!

## Storyboard

I created a quick storyboard for the animation. I opted for 3 crates opening instead of one as I thought it would be more interesting. In hindsight this was a bad idea as it completely blew out my render times.

I added some other loot items. The one on the left is just the blender default cube on an angle and the one on the right is some asset I had in my assets folder.
![](/lootbox/storyboard.gif)
## Background Set
I created a little set for my animation to take place in. I put a large window out to space and added some crates in the background.

![](/lootbox/SetGreybox.jpg)

I changed some of those crates into Sci-Fi looking shipping containers and shrunk the window a bit
![](/lootbox/SetGreybox2.png)

I textured the set using procedural materials. The walls are using a repeating brick texture parsed into a colour ramp and the floor is a diamond plate metal texture with some stains in world space.
![](/lootbox/set.jpg)
## Blocking
A little test animation in Blender's realtime renderer, Eevee.
Given that I was going to use Cycles for the final animation I darkened the scene to better see the reflections and indirect lighting before added some volumetric fog and the spotlights.
<video width= 100% src="/lootbox/blocking.mp4" controls> </video>

---


## The Animation
And after cooking my GPU for a few hours... Finally!
<video width= 100% src="/lootbox/lootboxfinal.mp4" controls> </video>

Unfortunately due to having 7x lights in the scene with volumetric fog each FRAME takes MINUTES to render, so I really don't have the time to make any adjustments and try again before handing it in. So due to the time constraints and audio not being part of the marking criteria, boxes sound like they are from Temu. I also wanted to make the boxes jump higher and rock a bit when they land so they don't feel so heavy and maybe brighten the scene a bit, It looks fine on my monitor but its too dark on the average screen.
