---
layout: post
title:  "Game dev, part 2 - AStarGrid2D"
date:   2024-10-27 11:45:00 +0300
---


navigation and pathfinding is one of the most important part of any game. When you try to move from point A to point B, you have a clear vision in your head how the unit is going to move. If somehow the unit doesn't move as you intended, you won't be happy. Even if the unit takes the correct path, but if it wasn't the one you actually wanted, we again get a angry player. For example, in some MOBA games, you can click anywhere on the minimap for the character to move. If you die because you miss clicked on the map, you will blame the game, not yourself. Luckily on turn-based games we can pre-show the route before actually moving! So let's get into the main topic, Godot's AStarGrid2D.

## A* star search  

On this post we won't focus on the [A* search algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm) rather on the Godot's [AStarGrid2D](https://docs.godotengine.org/en/stable/classes/class_astargrid2d.html) which uses A* for pathfinding. In the future we might need to use a different method for pathfinding, but currently this is good enough for now.


## Setting up AStarGrid2D  

Let's initiate a simple grid. We'll start by setting up the grid and later adding obstacles to avoid.

```gdscript
func _init_grid() -> void:
	var astar_grid = AStarGrid2D.new()

	# Do not allow diagonal movement. Paths can only move either vertically or horizontally.
	astar_grid.diagonal_mode = AStarGrid2D.DIAGONAL_MODE_NEVER

	# Define the grid area.
	# Starting point (0, 0)
	# End point  	(12, 12)
	astar_grid.region = Rect2i(0, 0, 12, 12)

	# set how tall and wide the tiles are.
	# 8 pixels wide, 8 pixels tall.
	astar_grid.cell_size = Vector2(8, 8)

	# Update the grid if region, cell_size or offset are changed.
	astar_grid.update()
```

The image is to help you imagine what's happening inside the grid. If you copy the code and run it in Godot, you won't actually see anything!

|![Grid image](/assets/images/grid.png)|
|:--:|
| *Starting grid. I'm making these pictures quickly with Matplotlib, so you need to use bit of your imagination* |

<br>

### Adding obstacles

Now with the grid, we need to add some obstacles here. We set those points as "solid", which tells the grid that those points can't be passed through, and need to be avoided. A solid point could be an enemy, a wall or anything that should be avoided.

```gdscript
func _init_grid() -> void:
	...
	for y in range(1,6):
    	astar_grid.set_point_solid(Vector2i(5, y))
	for x in range(6,11):
    	astar_grid.set_point_solid(Vector2i(x, 5))
```

|![Grid with obstacles image](/assets/images/obstacle.png)|
|:--:|
| *Grid with red wall obstacles* |

Now if we'd like to remove the solid points (Enemy moved, wall destroyed, etc.), setting it passable, we can use:
```gdscript
	astar_grid.set_point_solid(Vector2i(x, y), false)
```
<br>

### Getting the path

Now with the grid fully initiated, we can ask for a path. O is at `(7, 3)` and X is at `(4, 9)`.

```gdscript
func _init_grid() -> void:
	...
	var from: Vector2i = Vector2i(7,3)
	var target: Vector2i = Vector2i(4,9)
	var id_path: Array[Vector2i] = astar_grid.get_id_path(from, target)

	# [(7, 3), (6, 3), (6, 2), (6, 1), (6, 0),
	# (5, 0), (4, 0), (4, 1), (4, 2), (4, 3),
	# (4, 4), (4, 5), (4, 6), (4, 7), (4, 8),
	# (4, 9)]
	print(id_path)

	var point_path: PackedVector2Array = astar_grid.get_point_path(from, target)
	print(point_path)
	# [(56, 24), (48, 24), (48, 16), (48, 8), (48, 0),
	# (40, 0), (32, 0), (32, 8), (32, 16), (32, 24),
	# (32, 32), (32, 40), (32, 48), (32, 56), (32, 64),
	# (32, 72)]
```

The `.get_id_path(from, target)` method returns an array of point ID's. Now that may sound confusing, but super-simplified, you can think of them as references to the coordinates.

The `.get_point_path(from, target)` method then returns the exact points. Remember the `cell_size`? We can do notice that `(7, 3) * 8 = (56, 24)`!

|![Grid with obstacles image](/assets/images/path.png)|
|:--:|
| *Grid with red wall obstacles, blue path and green start- and end-positions* |

<br>

### Moving the object  

Now that you have the path, you can use them in your game! Simple example of using the coordinates to move the object:  

```gdscript
# We're using Node2D to access the 'position' property
extends Node2D
var current_id_path

# This runs at a fixed rate, we ignore the delta variable.
func _process(_delta: float) -> void:
	if current_id_path.is_empty():
    	return

	# We're using our tilemap to find where to move our object in the local coordinate space
	# '.pop_front()' remove and get the first ID
	var target_position = tile_map.map_to_local(current_id_path.pop_front())

	# This will instantly move the object to the coords.
	position = target_position
```

With this the object moves to the target position while avoiding the given obstacles! Now if you would use this, the object would move almost instantly to the target. In my game I use the following:

```gdscript
func _process(delta: float) -> void:
	if current_id_path.is_empty():
    	return
	var target_position = tile_map.map_to_local(current_id_path.front())

	# Because we have a list of coordinates where we have to move,
	# we check if we have arrived at the given coordinate.
	# If we have, we remove the coordinate from the array.
	if position.distance_to(target_position) < 1:
    	current_id_path.pop_front()

	# we move towards the point with the speed of 1.
	# In my game this makes the player move smoothly
	# towards the position.
	position = position.move_toward(target_position, 1)
```
