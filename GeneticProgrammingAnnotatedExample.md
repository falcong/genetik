# Genetic Programming Annotated Example #
This page describes the Genetic Programming example that comes with the library.
The Genetic Programming is more complicated than Genetic Algorithm (see the [Genetic Algorithm annotated example](GAAnnotatedExample.md)), because your individuals are represented as a tree, and you have to define each node (Gene) of tree.

This example will show how to use the framework to solve a simple regression function problem with the GP.
Our goal is to find the function x + x^3 given a set of (x,y) observations.
The nodes of the genetic programming tree are seen as basic functions (sum, product), so each Individual represents a tree of functions that approximates the observations.

## The Genes ##
Here is the code to define our genes
```
class Addition : public genetiK::gp::Gene
{
public:
	Addition() : gp::Gene(2){};
	gp::Gene*	copy() const { return new Addition(); }
	util::Variant evaluate(){
		return getArgument(0)->evaluate().getReal() + getArgument(1)->evaluate().getReal();
	}
};

class Multiplication : public gp::Gene
{
public:
	Multiplication() : gp::Gene(2){}
	gp::Gene*	copy() const { return new Multiplication(); };
	util::Variant evaluate(){
		return getArgument(0)->evaluate().getReal() * getArgument(1)->evaluate().getReal();
	}
};

class XVariable : public gp::Gene
{
private:
	double*	xPointer;	
public:
	XVariable(double *xPointer) : gp::Gene(0), xPointer(xPointer){}
	gp::Gene*	copy() const { return new XVariable(xPointer); }
	util::Variant evaluate(){
		return  *xPointer;
	}
};

```

Usually, to define a gene, you have to extend the ''gp::Gene'' and to override the ''copy'' and the ''evaluate'' virtual methods.
In the ''gp::Gene'' constructor you can specify the arity of the function, and in the ''evaluate'' you should write down the code that represents the function.
For examaple: the ''Addition'' gene in the ''evaluate'' simply compute the sum of its two children and returns it.

## The Individual Factory ##

The ''gp::IndividualFactory'' is slightly different than ''ga::IndividualFactory'', because it encapsulates even the genes and the tree creation.

```

class RegressionIndividualFactory : public gp::IndividualFactory
{
	private:
		double* xPointer;	
	public:
	RegressionIndividualFactory(unsigned char maxHeight, double* xPointer) : 
		gp::IndividualFactory(maxHeight,gp::GROW),
	xPointer(xPointer){}
	
	Individual* generate()
	{
		return new RegressionIndividual(this,xPointer);
	};
		
	gp::Gene* generateGene(EGenerateGeneFlags flags)
	{
		flags = flags;
		
		if (flags&TERMINAL && flags&FUNCTION){
			switch(util::Random::getImplementation()->getNextInt(7)){
				
				case 0:case 1:case 2:return new Addition();
				case 3:case 4:case 5:return new Multiplication();
				case 6:return new XVariable(xPointer);
			}
		}else if (flags&TERMINAL) return new XVariable(xPointer);
		else if (flags&FUNCTION){
			switch(util::Random::getImplementation()->getNextInt(2)){
				
				case 0:return new Addition();
				case 1:return new Multiplication();
			}			
		}
			
		util::Log::getInstance()->error("Error in generateGene");
		
		return NULL;
	}
};
```

The ''generate'' method simply returns a new istance of your ''gp::Individual'' implementation.
In order to use your genes, you must override the ''generateGene'' and return a new random gene according to the flags. The flags specify the arity of the node to be created, a ''TERMINAL'' node is a node without children, a ''FUNCTION'' node is a node with at least one child.
Note that the flags are ored, so flags could be ''TERMINAL'' and ''FUNCTION'' at the same time, in this case, you can return a completely random node.

## The Individual ##

The ''RegressionIndividual'' is very similar to the ''ExampleGAIndividual''.
The method ''fitness'' is the most important method, here you should evaluate the optimality of the solution.

In the Genetic Programming, you can access the ''root'' of the Genetic Tree, with the ''getRoot'' getter, and you can easily evaluate the tree.

In this example the fitness is the inverse of the variance.

```
class RegressionIndividual : public gp::Individual
{
	double*	xPointer;
public:
	RegressionIndividual(RegressionIndividualFactory* factory,double* xPointer) :
		gp::Individual(factory),
		xPointer(xPointer){}
		
	RegressionIndividual(const RegressionIndividual& individual) : gp::Individual(individual),xPointer(individual.xPointer){};
		
	/* Try to find f(x) = x^3 + x with 0 <= x < 1*/
	double fitness()
	{
		const unsigned char testCasesNumber=100;
		
		double quadraticDistance = 0;
		
		for (int i=0; i<testCasesNumber; i++){
			*xPointer = double(i)/testCasesNumber;
			double distance = getRoot()->evaluate().getReal() - (pow(*xPointer,3) + *xPointer );
			quadraticDistance += distance*distance;
		}
		
		quadraticDistance /= testCasesNumber;
		
		return 1/quadraticDistance;
	}	
	genetiK::Individual* copy() const{return new RegressionIndividual(*this);}
};
```

## Running the Evolutionary Algorithm ##

The ''EvolutionaryAlgorithm'' is created and runned in the ''GenetiProgramming::run'' method.
The code is very similar to the [wiki:AnnotatedExample ''GeneticAlgorithm'' example].

```
int GeneticProgramming::run(){
	double x=0;
	util::Log* log = util::Log::getInstance();
	log->setLevel(util::DEBUG);

	RegressionIndividualFactory* individualFactory = new RegressionIndividualFactory(6,&x);
	StopCriterionMaxIteration* stopCriterion = new StopCriterionMaxIteration(32);
	SelectionMethod* selectionMethod = new TournamentSelection(16);
	EvolutionaryAlgorithm alg(128,individualFactory,stopCriterion,selectionMethod);
	alg.run();
	log->info( alg.getPopulation()->getBest()->toString() );
}
	
```
