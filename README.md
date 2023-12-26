# UnityToGodot
This repository is meant to keep track of what needs to be done to port a Unity codebase to Godot. It is a **WORK IN PROGRESS**, and will include my findings and any tools/scripts that are useful in the process.


# Porting Steps
| Unity Attribute  | Godot Replacement | Notes |
| ------------- | ------------- | ----- |
| `.meta` files   | Remove all `.meta` files.  | On MacOS, search for .meta and delete. _TODO: create a script for this_  |
| `[SerializeField]`  | `[Export]`  |  |
| Auto serialized public properties | `[Export]`  | Unity auto-synthesizes public vars. Godot does not. Add [Export] to vars you want to serialize. |
| `void Awake()`  |  `public override void _Ready()` | Unity calls `Awake` with random order. Godot calls `_Ready` in order from child to parent to root. |
| `void Start()`  |  No exact equivalent |  Requires custom solution  |
| `void Update()` using `Time.deltaTime` for time step |  `public override void _Process(double delta)` using `delta` parameter for time step |  |
| `void FixedUpdate()` using `Time.fixedDeltaTime` for time step |  `public override void _PhysicsProcess(double delta)` using `delta` parameter for time step |  |
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
| Unity packages  |  _Remove all_  | Remove installed Unity packages from the Assets folder |
| n/a  | `partial` class requirement  | Because of the way Godot builds C# code, you'll get a build error for classes that subclass a built-in Godot class and don't use `partial`. Documentation: [Godot Interop With Source Generators](https://godotengine.org/article/whats-new-in-csharp-for-godot-4-0/#engine-interop-with-source-generators) |
| n/a  | Godot type name conflicts  | If you have any types in your code that conflict with built in Godot types, use `namespace` to wrap them.  |

_TODO: Will add more notes as I port more code_
