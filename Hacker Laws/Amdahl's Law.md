#programming #programming-philosophy #hacker-laws

[Amdahl's Law on Wikipedia](https://en.wikipedia.org/wiki/Amdahl%27s_law)

> Amdahl's Law is a formula which shows the _potential speedup_ of a computational task which can be achieved by increasing the resources of a system. Normally used in parallel computing, it can predict the actual benefit of increasing the number of processors, which is limited by the parallelisability of the program.

Best illustrated with an example. If a program is made up of two parts, part A, which must be executed by a single processor, and part B, which can be parallelised, then we see that adding multiple processors to the system executing the program can only have a limited benefit. It can potentially greatly improve the speed of part B - but the speed of part A will remain unchanged.

The diagram below shows some examples of potential improvements in speed:

[![Diagram: Amdahl's Law](https://github.com/dwmkerr/hacker-laws/raw/main/images/amdahls_law.png)](https://github.com/dwmkerr/hacker-laws/blob/main/images/amdahls_law.png)

_(Image Reference: By Daniels219 at English Wikipedia, Creative Commons Attribution-Share Alike 3.0 Unported, [https://en.wikipedia.org/wiki/File:AmdahlsLaw.svg](https://en.wikipedia.org/wiki/File:AmdahlsLaw.svg))_

As can be seen, even a program which is 50% parallelisable will benefit very little beyond 10 processing units, whereas a program which is 95% parallelisable can still achieve significant speed improvements with over a thousand processing units.

As [[../Hacker Laws/Moore's Law|Moore's Law]] slows, and the acceleration of individual processor speed slows, parallelisation is key to improving performance. Graphics programming is an excellent example - with modern Shader based computing, individual pixels or fragments can be rendered in parallel - this is why modern graphics cards often have many thousands of processing cores (GPUs or Shader Units).

See also:

- [[../Hacker Laws/Brooks' Law|Brooks' Law]]
- [[../Hacker Laws/Moore's Law|Moore's Law]]