---
title: C# wishlist - Internal interfaces
tags: csharp dotnet 
permalink: /blog/csharp-wishlist-internal-interfaces
---

I know that changing how C# deals with interfaces would be something hard to accomplish, and this implementation is quite "By the book",  but let me dream for a while and share this idea.

When you create an `internal interface`, what you are saying is that any class implementing that interface should be also internal. So we can't do something like this:

```cs
internal interface IClosable
{
    bool Close();
}

public class Connection: IClosable
{
    internal bool Close() {}
}
```

>Error CS0737: 'Connection' does not implement interface member 'IClosable.Close()'. 'IClosable.Close()' cannot implement an interface member because it is not public. (CS0737)

But, what if we change the definition and we say:

> Any class implementing an internal interface can be cast to that interface *inside* the assembly.

So, for instance, I could have a:

```cs
internal interface IClosable
{
    bool Close();
}

public class Connection: IClosable
{
    internal bool Close() {}
    public bool Open() {}
}
```

In this case, I should be able to treat my public `Connection` class as `IClosable` inside my assembly.

## Implementing internal interfaces explicitly

One workaround is through an explicit implementation. Let's review the definition of [Explicit Interface Implementation](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation):

>If a class implements two interfaces that contain a member with the same signature, then implementing that member in the class will cause both interfaces to use that member as their implementation.

Though the idea of an explicit implementation is not dealing with different scopes, it turns out that we can use it to implement an internal interface on a public class:

```cs
internal interface IClosable
{
    bool Close();
}

public class Connection: IClosable
{
    internal bool Close() {}    
    public bool Open() {}

    bool IClosable Close() => Close();
}
```

As you can see, it requires some boilerplate. The explicit method needs to call the internal method.

What are your thoughts?

Don't stop coding!