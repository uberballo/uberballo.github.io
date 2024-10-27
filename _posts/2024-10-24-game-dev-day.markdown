---
layout: post
title:  "Game dev, part 1"
date:   2024-10-24 17:45:00 +0300
---


I've always wanted to make my own game. I think I've started a handful of game projects and they have never taken off. This time, the first commit was made on 09-10-2024 and I'm still working on it, so it's looking good! This being my first time making a game, I'm sure there are some choices that I'm going to regret, but that's future-me's problem. Let's look at what I'm doing, why and how.



## Beginning of the roguelike  

I've always enjoyed playing roguelikes where the randomness and permaloss were a crucial part of the game. Games like [Dungeon Crawl - Stone Soup](https://crawl.develz.org/) where you have multiple different ways to build your own character, explore procedurally generated dungeons and try to survive as long as possible. I want to recreate the same experience but with a twist of my own! The twist? It's yet to be announced...


I'm using [Godot](https://godotengine.org/) with [GDScript](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html). The game is Topdown, turn-based, 2D pixel graphics, procedurally generated roguelike with RPG elements. Yes, yes it's mouthful but that's the main idea in a nutshell. When you read that, you know what you're getting yourself into.

## Why GDScript and not C#?  

Godot supports two languages, GDScript and C#. It's the first choice you have to make when you're making your own game. I won't delve to deep into the pro's and con's of these languages, but simply C# has better performance but not as popular as GDScript and thus doesn't support all Godot's features. Performance isn't going to be an issue for my game, and if it is, I can always use [GDExtension](https://docs.godotengine.org/en/stable/tutorials/scripting/gdextension/index.html) to run for example C++ modules.

## First commit

The first commit contained quite a bit of features. While nothing groundbreaking, there was working A-star navigation, player and enemy nodes, simple state-machine for the enemy behavior, a simple map with collision (Collision in turn-based game is a topic for another time) and simple mouse movement. It was really nice to see some real movement in the game. At this point I did have grand ideas for the game, but as the development progressed, I quite quickly realized that it's not going to be as easy.

|![Source image](/assets/images/first_version.png)|
|:--:|
| *Initial commit’s version with debug borders showing* |


That's it for the first commit. While the game is still quite far away from being fun, I've enjoyed this journey thus far. There have been times when I've been quite confused with Godots containers, nodes and resources. When I read about "Each node is a class" and I'm just like, why? Sometimes I forget it and everything is going smoothly, sometimes I want to achieve something that isn't as easy as in other languages. By the second week everything was a bit more clearer, so never give up!

## Next time

There wasn't really anything that interesting in this post, as it was more of an "setting the table" for future posts. I'm currently working on main gameplay mechanics and adding procedurally generated maps to the game.
