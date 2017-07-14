### Unity and C# Cheatsheet
The following are useful code snippets, design patterns, and best practices that I've found are handy to reference while developing for Unity3D. Your mileage may vary.

#### Editor

##### Add a custom Event Handler in Inspector.
```c#
using UnityEngine.Events;

public UnityEvent customEvent;

public void SomeFunction() {
	customEvent.Invoke();
}
```
[Unity Documentation](https://docs.unity3d.com/Manual/UnityEvents.html)

##### Platform Dependent Compilation
+ preprocessor directives that let you partition your scripts to compile and execute a section of code exclusively for one of the supported platforms

```c#
#if UNITY_EDITOR
	some_function();
#endif
```
In the above, the `some_function();` code only exists when in the Unity Editor and will not exist whatsoever on runtime or when you compile. See [Unity Documentation here](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html)