---
title: Reading Complex Codebase
category: notes
publishDate: 2025-01-02
blogTitle: Contributing to Complex Projects
blogAuthor:
blogLink: https://mitchellh.com/writing/contributing-to-complex-projects
---

I came across this article when I was trying to read [arktype](https://github.com/arktypeio/arktye) code base for learning about typescript performance.

His approach to read new and complex code base is trace down, learn up. He starts with public API or a feature(could be function or a class) which he has used before. Trace that API, reaching its core until it can't be broken down further. One thing to remember not to understand at this point just taking notes how the API is structured, the overall abstraction. Now begin learning from the bottom to up. 

I must say it works, I have tried it. Must better than reading like code base like a book.

Another thing he suggest doing is experiment and break things to learn how things work. It makes understanding easier and quicker, instead of reading blocks of code and try to understand just remove a piece or change it.

> The first step to understanding the internals of any project is to become a user of the project.

Trace Down
>I start with a feature or use case, and start from the outside in to trace the codepath that the feature follows. During this process, I take notes about the files, lines, and functions I’m going through, but I do not yet attempt to understand how anything works. This is the “trace down” phase.

> From the tracing notes, you can usually get a “big picture” of how something works. Based on filenames, functions, etc. you can usually start to discern major subsystems of a project. This helps later compartmentalize the learning process into more reasonably sized chunks.

> Don’t try to learn everything. A common mistake I see people make is getting lost for weeks or months and ultimately getting discouraged because they attempt to just read an entire project line by line. Stay focused and learn feature by feature.

Learn up
> I choose the innermost point to start because it is usually the most fundamental and least abstracted. As you climb layers, it tends to correlate with growing levels of abstraction making it harder to learn if you don’t understand the component pieces.

Experiment and Break Things
> During the “trace down, learn up” process, I find it very helpful to experiment and break things to learn how something works. This is why it is critical to learn how to build a project before attempting to read the internals.
> Add new log statements, implement tiny chunks of new functionality, change existing functionality, etc. then rebuild the project and see what happens. This is also a good way to really test your understanding of how something works.

Make a Bit-sized change
> The hardest part is usually finding a small change to make. There is no silver bullet here. I browse the issues looking for something that looks contributor friendly. I usually make a few false starts or abandon an issue entirely and try others. Finally, I’ll find one. The time it takes to find the issue or even fix the issue may be frustratingly long, but its the price of admission. Projects often have "contributor friendly" labels to help guide new contributors.


And finally this for me or for you as well
> I think too many engineers look at stereotypically complex projects such as programming languages, browsers, databases, etc. as magic or as destined for higher-beings. I like to remember that all projects were started by other humans. If they could do it, I can do it too. And so can you.

