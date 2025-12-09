---
author: Alex Figar
title: SciFi Crate Animation
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
For a TAFE assignment I was tasked with creating a loot box animation for a SciFi game. The assignment required me to create 1x loot box and 1x piece of rare loot and document the creative process through concept art, story boarding and blocking.
<!--more-->

## Concept Art

 For the crate I really just added low angular lines to a cube  to achieve the "Sci Fi" look and went from there. Clustered most details around the top where the box opens but still added some bevels and chamfers to the bottom.

There was a lava lamp in the room I was working in and I thought it kind of looked life an energy cell looking thing... So now the rare loot is an energy cell looking thing with a particle effect in the centre.

| ![](/lootbox/conceptartbox.jpg) | ![](/lootbox/conceptartitem2.jpg) |
| ------------------------------- | --------------------------------- |

## Grey Box
Bringing the 2D drawings to life in 3D

| ![](/lootbox/GreyBoxOpen.png) | ![](/lootbox/GreyBoxLoot.png) |
| ----------------------------- | ----------------------------- |
## Wireframe

I added the locking mechanism on to the notch of the loot box and I forwent the energy particles in favour of a mini blackhole but without its accretion disk it looks pretty bad.

| ![](/lootbox/LootBoxWireframe.png) | ![](/lootbox/RareLootWireframe.png) |
| ---------------------------------- | ----------------------------------- |

## Better Blackhole Shader

In an attempt to remedy the lacking blackhole effect I created a new blockhole material and an accretion disk.
This is unfortunately way too expensive to render in an animation using cycles so I need to find a cheaper one but it looks sick.

![](/lootbox/blackhole.jpg)

## First Renders

The accretion disk is now a seperate plane with a texture applied to it so it doesn't take forever to render this animation and the rare loot has a much nicer brushed copper material.

| ![](/lootbox/FirstRenderBox.png) | ![](/lootbox/FirstRenderLoot.png) |
| -------------------------------- | --------------------------------- |

## Baking Textures

After unwrapping the models and making sure their texel density was ok I baked out various maps to make rendering the animation faster.

Finally!

<video width= 100% src="/lootbox/lootboxfinal.mp4" controls> </video>

Audio was not part of the marking criteria so the boxes sound like they are from Temu.