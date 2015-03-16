# GenetiK (Genetic Kernel) #
GenetiK is a generic framework that supports [evolutionary algorithms](http://en.wikipedia.org/wiki/Evolutionary_algorithm).

According to the evolutionary approach, solutions to particular problems can be represented as individuals of a population. For each individual, a particular function, called ''fitness'', can be defined to associate a high fitness scores with 'good' solutions.

Starting from any set of individuals, evolving this population (with a process that loosely resembles darwinian selection in biology) results, after a sufficient number of ''generations'', in a new population of high-fitness individuals.
In other words, starting from any set (even randomly generated) of potential solutions to a problem, it is possible to obtain new sets of progressively 'better' solutions, eventually resulting in the selection of an optimal (or near-optimal) solution.

Therefore, depending on the nature of the problem to be solved, it is possible to find a good solution by expressing candidate solutions and their fitness in an appropriate way.
Even adopting the same evolutionary algorithm, but two different representations of individuals and fitness, can lead to dramatically different results, therefore finding a good representation of individuals and their fitness is crucial to the success or failure of a particular application.

The idea behind the GenetiK project is to let users experiment with evolutionary computation without having to code from scratch the algorithm that deals with the evolution itself, but rather let them concentrate on their specific tasks and objectives.
This allows users to focus on finding good representations for candidate solution, the best ways to measure fitness and other aspects, but, at the same time, allowing them to fine-tune the algorithm to their specific needs by customizing its behaviour, if required.

## Project Structure ##
The GenetiK framework is divided in 5 subprojects:
  * **genetiK** -- that includes the common base classes
    * **genetiK::ga** -- specialized in dealing with [Genetic Algorithms](http://en.wikipedia.org/wiki/Genetic_algorithm)
    * **genetiK::gp** -- oriented towards [Genetic Programming](http://en.wikipedia.org/wiki/Genetic_programming)
      * **genetiK::gp::st** -- dedicated to Strongly Typed Genetic Programming
    * **genetiK::util** -- includes all the utility classes

[Here](http://genetik.googlecode.com/svn/trunk/doc/genetik_diag.png) you can find an UML diagram describing the structure of the framework. ([Source XMI file](http://genetik.googlecode.com/svn/trunk/doc/GenetiK.xmi), generated with [Umbrello](http://uml.sourceforge.net/))

![![](http://genetik.googlecode.com/svn/trunk/doc/genetik_diag_s.png)](http://genetik.googlecode.com/svn/trunk/doc/genetik_diag.png)

**genetiK** provides the common evolutionary algorithms functionalities, while **genetiK::ga**, **genetiK::gp** and **genetiK::gp::st** contains the specific classes that should be extended to develop a specific solution. You can consult the [annotated example](GAAnnotatedExample.md) (the genetic algorithm that comes with the library) to see how the framework can be used.


## Documentation ##
Please refer to [HTML documentation](http://casta.novlet.com/genetik/doc/modules.html) for more information.

## Getting the code ##
To check out the current version of GenetiK use
```
svn checkout http://genetik.googlecode.com/svn/trunk/ genetik
```

and compile with
```
make
```

_(We hope that your current platform is supported ![http://forum.tgmonline.it/forum/images/smiley_icons/icon_lol.gif](http://forum.tgmonline.it/forum/images/smiley_icons/icon_lol.gif) )_

## Running the example ##
After compiling the library, you can run the example by typing
```
./bin/example
```
