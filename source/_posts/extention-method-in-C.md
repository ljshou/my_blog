title: 'extention-method-in-C#'
tags:
categories: C#
---
Extension methods allow an existing type to be extended with new methods without altering the definition of the original type. 
<!-- more -->
### Definition
An extension method is a static method of a static class, where the *this* modifier is applied to the first parameter. The type of the first parameter will be the type that is extended. 

### Example: 
```cs
public static class StringHelper 
{  
	public static bool IsCapitalized (this string s)  
	{    
		if (string.IsNullOrEmpty(s)) return false;    
		return char.IsUpper (s[0]);  
	} 
}

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("ljshou".IsCapitaliszed());
        Console.WriteLine("Ljshou".IsCapitaliszed());
        Console.Read();
    }
}
```

### Deep dive
The IsCapitalized extension method can be called as though it were an instance method on a string, as follows: 
```cs
Console.WriteLine ("Perth".IsCapitalized()); 
```
An extension method call, when compiled, is translated back into an ordinary static method call: 
```cs
Console.WriteLine (StringHelper.IsCapitalized ("Perth"));
```

 The translation works as follows: 
 ```cs
 arg0.Method (arg1, arg2, ...);// Extension method call 
 StaticClass.Method (arg0, arg1, arg2, ...); // Static method call 
 ```

 ### Reference
 C# 5.0 in a Nutshell