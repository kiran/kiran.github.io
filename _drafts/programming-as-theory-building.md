---
title: (paper report) Programming as Theory Building
layout: "post"
permalink: programming-as-theory-building
category: computers
tags: [papers]
---

- Peter Naur. [Programming as Theory Building][patb]. (1985)

In this paper, Peter Naur asserts that programming is the act of building a model of the system, similar to how physics can be described as building a theory or model that explains the real world. Essentially, programmers strive to design understandable abstractions, and understanding the structure of these abstractions is essential to working on a program. This is in contrast to the view of programming as generating a set of instructions that does a thing -- as symbol manipulation, in essence.

Naur also asserts that the act of modifying and expanding programs is an important part of programming. It's rare that you write a system to do a thing, and leave it at that -- you're almost always expanding the feature set, or its stability. In the Theory Building Model, changing a program is expanding the scope of things you want to represent in your little symbolic universe. Making changes thus requires understanding the abstractions and theory that your universe is constructed on, so you can slot your new changes in the correct places.

Naur describes a team attempting to add functionality to a compiler without understanding its underlying theory, which really resonated with me:

> the solutions suggested by group B were found by group A to make no use of the facilities that were not only inherent in the structure of the existing compiler but were discussed at length in its documenation, and to be based instead on additions to that structure in the form of patches that effectively destroyed its power and simplicity.

In another example of this:

> the original powerful structure was still visible, but made entirely ineffective by amorphous additions of many different kinds.

I'm imagining Howl's Moving Castle here: http://film110.pbworks.com/f/1260336766/howlscastle.jpg

http://film110.pbworks.com/f/1260336766/howlscastle.jpg

In both these cases, the original team went to great lengths to provide documentation and source code, but those were insufficient to convey the structure of the underlying system.

The paper's writing is dense and difficult to parse, but I really resonated with (almost) all of his views.

## Similar Papers (where to go from here)

I have no idea! I'd love to hear suggestions! :D

[patb]: http://pages.cs.wisc.edu/~remzi/Naur.pdf
