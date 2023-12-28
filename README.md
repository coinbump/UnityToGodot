# UnityToGodot
This repository is meant to keep track of what needs to be done to port a Unity codebase to Godot. It is a **WORK IN PROGRESS**, and will include my findings and any tools/scripts that are useful in the process.

## Porting Steps
| Unity Concept | Godot Replacement | Notes |
| ------------- | ------------- | ----- |
| `.meta` files   | Remove all `.meta` files  | On MacOS, search for .meta and delete. _TODO: create a script for this_  |
| `[SerializeField]`  | `[Export]`  |  |
| Auto serialized public fields | `[Export]`  | Unity auto-synthesizes public fields. Godot does not. Add [Export] to fields you want to serialize. |
| `void Awake()`  |  `public override void _Ready()` | Unity calls `Awake` with random order. Godot calls `_Ready` in order from child to parent to root. |
| `void Start()`  |  No exact equivalent |  Requires custom solution  |
| `void Update()` using `Time.deltaTime` for time step |  `public override void _Process(double delta)` using `delta` parameter for time step |  |
| `void FixedUpdate()` using `Time.fixedDeltaTime` for time step |  `public override void _PhysicsProcess(double delta)` using `delta` parameter for time step |  |
| `.zero`  | `.Zero`  |  |
| implicit conversion between `Vector3` and `Vector2`  | Not supported |  |
| `[Range(0, 20)]`  | `[Export(PropertyHint.Range, "0,20,")]`  |  |
| `radians = degrees * Mathf.Deg2Rad`  | `radians = Mathf.DegToRad(degrees)`  |  |
| `degrees = radians * Mathf.Rad2Deg`  | `degrees = Mathf.RadToDeg(radians)`  |  |
| `Random.InitState(seed)`  | `GD.Seed(seed)`  | Initialize random number generator seed |
| `Random.Range(min, max)`  | `GD.Randi() % valueRangeSize + minValue`  | Generate random integer |
| `Random.value`  | `GD.Randf()`  | Generate random float between 0-1.0f (inclusive) |
| `gameObject.name`  | `node.Name`  | In Godot, the names of sibling nodes must be unique from each other. If you change the node name to an existing name it will be automatically renamed (_From in-code documentation_). |
| `.x`  | `.X`  | Vector types |
| `.y`  | `.Y`  | Vector types |
| `.up`  | `.Up`  | Vector types |
| `.down`  | `.Down`  | Vector types |
| `.z`  | `.Z`  | Vector3 |
| `.forward`  | `.Forward`  | Vector3 |
| `.normalized`  | `.Normalized()`  | Vector types |
| `.magnitude`  | `.Length()`  | Vector types |
| `GameObject`  | `Node`  | _See Porting Problems section for more information_ |
| `Vector2Int`  | `Vector2I`  |  |
| `Vector3Int`  |  `Vector3I` |  |
| `transform.childCount`  |  `GetChildCount()` | Get count of children for MonoBehaviour (Unity) vs Node (Godot) |
| `foreach (Transform childTransform in transform)`  |  `foreach (var child in GetChildren())` | Iterate through children for MonoBehaviour (Unity) vs Node (Godot) |
| `OnTriggerEnter2D`  | _See Godot Code Sample: Detect 2D Collisions_ | |
| `OnCollisionEnter2D`  | _See Godot Code Sample: Detect 2D Collisions_ | |
| `using UnityEditor;`  | _Remove all_ |  |
| `using UnityEngine.XXX;`  | _Remove all_ |  |
| `using UnityEngine;`  | `using Godot;` |  |
| `Debug.Log`  | `GD.Print` |  |
| Input Actions | `InputEvent`/`InputMap` | Godot Documentation: https://docs.godotengine.org/en/stable/tutorials/inputs/inputevent.html |
| `Rigidbody2D` as child component | `RigidBody2D` as parent node or `CharacterBody2D` as parent node | In Godot, the roles of ownership between a node and its rigidbody are reversed |
| Unity packages  |  _Remove all_  | Remove installed Unity packages from the `Assets/plugins` folder |
| n/a  | `partial` class requirement  | Because of the way Godot builds C# code, you'll get a build error for classes that subclass a built-in Godot class and don't use `partial`. Documentation: [Godot Interop With Source Generators](https://godotengine.org/article/whats-new-in-csharp-for-godot-4-0/#engine-interop-with-source-generators) |
| n/a  | Godot type name conflicts  | If you have any types in your code that conflict with built in Godot types, use `namespace` to wrap them. |
| Prefab  | Scene | Documentation: https://docs.godotengine.org/en/3.1/getting_started/editor/unity_to_godot.html#where-are-my-prefabs |

## Godot Limitations
| Summary | Problem Statement | Solution |
| ------------- | ------------- | ----- |
| No visual scene while running | In Unity, you can view a scene visually in the editor. In Godot, you can see the list of nodes in the Scene tree while running in the Remote tab, but you can't view the objects visually in the scene editor. | |

## Porting Problems
| Summary | Problem Statement | Solution |
| ------------- | ------------- | ----- |
| Components vs Nodes | In Unity, each `GameObject` can have multiple Components (via `MonoBehaviour`). In Godot, each Node is the component. | Create a separate node for each behavior, instead of a separate component. This requires rethinking logic around finding the target for a particular behavior. Instead of using `gameObject` directly, in Godot you might operate on the parent node, for example. |
| No transform in Node | In Unity, every GameObject has a transform. In Godot, Node doesn't have a transform, so you're forced to choose if your code subclasses `Node2D` or `Node3D`. | Duplicated code for `Node2D` vs `Node3D` or utility structs that can operate on either a `Node2D` or `Node3D`. Example: Transform tool that can set the position of either node type. |
| 2D Physics `Sprite2D` not working | After adding `RigidBody2D`, sprite doesn't move or interact with physics. | Sprite must be child node of `RigidBody2D`. This is different than Unity, where the RigidBody component is attached to the GameObject along with the sprite renderer. |
| Strange scaling for physics sprite | When adding `RigidBody2D` to a `Sprite2D` you might see strange scaling when the game is running. | Check all Node2D transforms in parent nodes of the sprite and make sure they are (1, 1). Documentation: https://stackoverflow.com/questions/77590667/godot-physics-resizing-my-sprites-at-runtime |

## Code Samples
### Godot Code Samples
#### Detect 2D Collisions
```csharp
var collision = characterBody2D.MoveAndCollide(velocity);
```

**or**

```csharp
characterBody2D.Velocity = velocity;
characterBody2D.MoveAndSlide();
for (int i = 0; i < characterBody2D.GetSlideCollisionCount(); i++)
{
    var slideCollision = characterBody2D.GetSlideCollision(i);
    GD.Print("Collided with: ", (slideCollision.GetCollider() as Node).Name);
}
```

## Troubleshooting
| Problem | Possible Solutions | Notes |
| ------------- | ------------- | ----- |
| Trouble with VSCode and Intellisense   | ?Reinstall .NET SDK? | _Unverified. Still Investigating_ |

_TODO: Will add more notes as I port more code_
