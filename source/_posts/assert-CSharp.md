title: assert in C#
date: 2015-10-03 21:00:54
tags: C#
categories: C#
---
Assert methods are pretty useful for debugging during development.  
*It is believed a good practice to use Assert methods.*  
Like many other languages like C++, Python, .Net supports several versions of Assert methods.
<!--more-->

### Assert Methods
|Name  |Description|
|---------------|-----------|
|Assert(boolean)|Checks for a condition; if the condition is **false**, displays a message box that shows the call stack.|
|Assert(Boolean, String)|Checks for a condition; if the condition is **false**, outputs a specified message and displays a message box that shows the call stack.|


### Syntax
**Namespace**:   System.Diagnostics  
**Assembly**:  System (in System.dll)  

Take Assert(Boolen) as an example.
```
[ConditionalAttribute("DEBUG")]
public static void Assert(
	bool condition
)
```


### Example
```cs
using System;
using System.Diagnostics;
#define DEBUG
public class Test 
{
	static void Main(string[] args)
	{
		Debug.Assert(1 == 2);
		Console.ReadKey();
	}
}
```

### Reference
https://msdn.microsoft.com/en-us/library/System.Diagnostics.Debug_methods(v=vs.110).aspx
