# Annotated Example #
This page describes the Genetic Algorithm example that comes with the GenetiK library. Its purpose is to show how the framework can be used with a toy example.

The objective of the example algorithm is to find the sequence of bits with the maximum numbers of **1s**. It is a trivial task, but a significant example of how a problem can be easily formulated and solved using an approach based on Genetic Algorithms.

Intuitively, the most important step we must take in order to achieve our goal is to define an appropriate fitness function. It is obvious that the best choice for this is to simply "count" the number of bits that are set to 1 in the current individual. This is not the only task to do, however, and in the next few paragraphs we will show what needs to be done to build a complete, working and functional genetic algorithm (and, _yes_, it works!).

## The Individual Factory Class ##
The _ExampleGAIndividualFactory_ is quite simple: it extends the standard GA Individual Factory, defining its specific constructor (in this case, it adds nothing to the parent class), and overriding the _generate_ method.
```
class ExampleGAIndividualFactory : public ga::IndividualFactory {

public:
	ExampleGAIndividualFactory::ExampleGAIndividualFactory(unsigned int length) :
	ga::IndividualFactory(length)
	{
	}

	Individual* ExampleGAIndividualFactory::generate(){
		return new ExampleGAIndividual(getLength());
	}
};
```
In particular, the constructor calls the parent's one, taking as argument the length of the Individuals to generate. The _generate_ call does nothing more than creating a new _ExampleGAIndividual_ of the specified length.

## The Individual Class ##
The _ExampleGAIndividual_ is the core of our algorithm: it encapsulates the logic of our problem using the _fitness_ function and some accessory methods.
```
class ExampleGAIndividual : public ga::Individual {

public:
	ExampleGAIndividual::ExampleGAIndividual(unsigned int length) :
	genetiK::ga::Individual(length)
 	{
	}

	ExampleGAIndividual::ExampleGAIndividual(const ExampleGAIndividual& individual) :
	genetiK::ga::Individual(individual)
 	{
	}

	double ExampleGAIndividual::fitness() {
		double fitness = 0;

		const int blockSize = sizeof(unsigned int)<<3;

		for (int i = 0; i < blockNum; ++i) {
			int last = i < blockNum - 1 || !(length%blockSize) ? 0 : blockSize - length%blockSize;
			int tempBlock = bitArray[i];
			for (int j = (sizeof(int)<<3) - 1; j >= last; --j) {
				if (((tempBlock >> j) & 1))
					++fitness;
			}
		}
	
		return fitness;
	}

	genetiK::Individual* ExampleGAIndividual::copy() const {
		return new ExampleGAIndividual::ExampleGAIndividual(*this);
	}
};
```
The _Individual_ class is based on the standard GA Individual: this means that we do not need to re-implement the crossover and mutation operators, because they are provided by the framework (we could however, redefine them, if needed).
The only things that we have to implement are the methods listed above. In particular:
  * the length-based constructor uses the parent's one, and creates an Individual setting its bits randomly
  * the copy constructor and the _copy_ function are required by the algorithm, even if, since in this case, the Individual does not have additional data members, the do not perform any operation
  * the _fitness_ function works exactly as anticipated: it counts the number of 1s in the current individual

## Initializing the Evolutionary Algorithm ##
The initialization is performed by the _run_ method, that creates the appropriate individual factory, stop criterion and selection methods, and uses them to create the new Evolutionary Algorithm instance, that encapsulates the typical genetic algorithm.
```
int GeneticAlgorithm::run(){
	
	util::Log* log = util::Log::getInstance();
	log->setLevel(util::DEBUG);

	ExampleGAIndividualFactory* individualFactory = new ExampleGAIndividualFactory(32);
	StopCriterionMaxIteration* stopCriterion = new StopCriterionMaxIteration(16);
	RouletteWheel* rouletteWheel= new RouletteWheel();
	
	EvolutionaryAlgorithm alg(64,individualFactory,stopCriterion,rouletteWheel);
	
	alg.run();

	log->info( alg.getPopulation()->getBest()->toString() );
	
	return 0;
}
```
As we can see, in this specific example:
  * 32 is passed as an argument to the ExampleGAIndividualFactory constructor, resulting in the creation of 32-bit individuals
  * the stop criterion is a StopCriterionMaxIteration, with 16 as an argument of the constructor, therefore, the algorithm will stop after 16 iterations
  * RouletteWheel is chosen as selection method
  * the EvolutionaryAlgorithm instance is created, using the former parameters, and passing 64 as first argument: this will result in a population of 64 individuals

## Conclusion ##
In this example, we showed all the steps that are needed to create a specific instance of a user-defined Genetic Algorithm. The final program is able to find quite easily the optimal solution, and it takes just a small effort (e.g. redefine the _fitness_ function) to change its behaviour to solve less trivial problems.
Easy, isn't it?
Now you are ready to created your own experiments with the GenetiK framework. But before, be sure to check out also the [GeneticProgramming](GeneticProgrammingAnnotatedExample.md) and StronglyTypedGeneticProgramming examples.