# UnityToGodot
This repository is meant to keep track of what needs to be done to port a Unity codebase to Godot. It is a **WORK IN PROGRESS**, and will include my findings and any tools/scripts that are useful in the process.


# Porting Steps
| Unity Concept  | Godot Replacement | Notes |
| ------------- | ------------- | ----- |
| `.meta` files   | Remove all `.meta` files.  | On MacOS, search for .meta and delete. _TODO: create a script for this_  |
| `[SerializeField]`  | `[Export]`  |  |
| Auto serialized public properties | `[Export]`  | Unity auto-synthesizes public fields. Godot does not. Add [Export] to fields you want to serialize. |
| `void Awake()`  |  `public override void _Ready()` | Unity calls `Awake` with random order. Godot calls `_Ready` in order from child to parent to root. |
| `void Start()`  |  No exact equivalent |  Requires custom solution  |
| `void Update()` using `Time.deltaTime` for time step |  `public override void _Process(double delta)` using `delta` parameter for time step |  |
| `void FixedUpdate()` using `Time.fixedDeltaTime` for time step |  `public override void _PhysicsProcess(double delta)` using `delta` parameter for time step |  |
| `.zero`  | `.Zero`  |  |
| `[Range(0, 20)]`  | `[Export(PropertyHint.Range, "0,20,")]`  |  |
| `radians = degrees * Mathf.Deg2Rad`  | `radians = Mathf.DegToRad(degrees)`  |  |
| `degrees = radians * Mathf.Rad2Deg`  | `degrees = Mathf.RadToDeg(radians)`  |  |
| `Random.Range(min, max)`  | `GD.Randi() % valueRangeSize + minValue`  | Generate random integer |
| `Random.InitState(seed)`  | `GD.Seed(seed)`  | Initialize random number generator seed |
| `.x`  | `.X`  | All vector types |
| `.y`  | `.Y`  | All vector types |
| `.z`  | `.Z`  | All vector types |
| `.up`  | `.Up`  | All vector types |
| `.down`  | `.Down`  | All vector types |
| `.forward`  | `.Forward`  | Vector3 |
| `.normalized`  | `.Normalized`  |  |
| `GameObject`  | `Node`  |  |
| `Vector2Int`  | `Vector2I`  |  |
| `Vector3Int`  |  `Vector3I` |  |
| `using UnityEdtor;`  | _Remove all_  |  |
| `using UnityEngine.XXX;`  | _Remove all_  |  |
| `using UnityEngine;`  | `using Godot;`  |  |
| `Debug.Log`  | `GD.Print`  |  |
| Unity packages  |  _Remove all_  | Remove installed Unity packages from the `Assets/plugins` folder |
| n/a  | `partial` class requirement  | Because of the way Godot builds C# code, you'll get a build error for classes that subclass a built-in Godot class and don't use `partial`. Documentation: [Godot Interop With Source Generators](https://godotengine.org/article/whats-new-in-csharp-for-godot-4-0/#engine-interop-with-source-generators) |
| n/a  | Godot type name conflicts  | If you have any types in your code that conflict with built in Godot types, use `namespace` to wrap them.  |

_TODO: Will add more notes as I port more code_
