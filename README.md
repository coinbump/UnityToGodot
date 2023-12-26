# UnityToGodot
This repository is meant to keep track of what needs to be done to port a Unity codebase to Godot. It is a **WORK IN PROGRESS**, and will include my findings and any tools/scripts that are useful in the process.


# Porting Steps
| Unity Attribute  | Godot Replacement | Notes |
| ------------- | ------------- | ----- |
| `.meta` files   | Remove all `.meta` files.  | On MacOS, search for .meta and delete. _TODO: create a script for this_  |
| `[SerializeField]`  | `[Export]`  |  |
| Auto serialized public properties | `[Export]`  | Unity auto-synthesizes public vars. Godot does not. Add [Export] to vars you want to serialize. |
| `.zero`  | `.Zero`  |  |
| `.x`  | `.X`  |  |
| `.y`  | `.Y`  |  |
| `.z`  | `.Z`  |  |
| `.normalized`  | `.Normalized`  |  |
| `GameObject`  | `Node`  |  |
| `Vector2Int`  | `Vector2I`  |  |
| `Vector3Int`  |  `Vector3I` |  |
| `using UnityEdtor;`  | _Remove all_  |  |
| `using UnityEngine.XXX;`  | _Remove all_  |  |
| `using UnityEngine;`  | `using Godot;`  |  |
| `Debug.Log`  | `GD.Print`  |  |

_TODO: Will add more notes as I port more code_
