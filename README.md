![Alt text](/images/img.png "MobsPy")

# MobsPy

Welcome to the Meta-Species Oriented Biosystem Syntax in Python repository. Mobspy was invented to facilitate the design of complex Chemical Reaction Networks. In this repository, one can find the first implementation of this language and a simulation tool to simulate models generated by it. The simulation tool has both deterministic and stochastic compatibility.

# Getting started 

To get started, you only need to pip install:

	pip install mobspy 

And check out some example tutorial models in either the example_model folder or the notebook directory in this repository.

# How it works

Meta-Species are sets of species. Using them allows users to assign reactions to the entire set or a subset by querying. The Meta-Species are based around a reaction inheritance system and independent state spaces.

## Basic syntax

To create species from scratch, use the BaseSpecies function that takes as an argument the number of species one wants to make. To assign rates, one can define reactions using the '>>' operator and the brackets '[]'. To assign counts, use the call operator. An elementary example is the following:

	A, B, C, D = BaseSpecies(4)
	A(200) + B(100) >> 2*C + D [420]
	MySim = Simulation(A | B | C | D)
	MySim.run()

## Inheritance

For instance:

	Mortal = BaseSpecies()
	Mortal >> Zero [1]

The reaction above is a death reaction where the Meta-Species Mortal is dying. Zero is the MobsPy variable for representing nothing. We can design new Meta-Species from other Meta-Species using either the multiplication or the New() constructor. 

	Replicator, Triplicator = New(Mortal, 2)
	Replicator >> 2*Replicator [1]
	Triplicator  >> 3*Triplicator [1]
	Multiplicator = Replicator*Triplicator 

In the code above, one can visualize the inheritance mechanism. Here both Replicator and Triplicator inherit from Mortal. Therefore, they also receive a death reaction. Multiplicator inherits from Replicator and Triplicator, and therefore from Mortal too. So Multiplicator now has three reactions, the death reaction, the duplication reaction, and the triplication reaction. 

## Indepedent State Spaces

Each Meta-Species has a set of states. One can add states to species by using the dot command ('.state') or by inheritance. A Meta-Species that inherits from another gains access to its states. 
For instance:

	Horned, Color = BaseSpecies(2)
	Horned.small_horn >> Horned.big_horn [1]
	Color.white >> Color.rainbow [1]
	Unicorn = Horned*Color
	Unicorn.rainbow >> 2*Unicorn.white [1]

Unicorn has the states of both Horned and color in the code above and their reactions. Unicorn species will be formed by the name followed by a dot and a state for each species inheritors for all possible combinations. So here we have the species - Unicorn.small_horn.white, Unicorn.big_horn.white, Unicorn.small_horn.rainbow, Unicorn.big_horn.rainbow. 

The states can be used in the reactants to assign a reaction only to the correct subset. Here only the rainbow unicorns can duplicate. Finally, the states on the products refer to transformations of states that originate from the same Meta-Species. So here, all the rainbow Unicorns become white after multiplying since both states come from the Meta-Species color. 

# Simulation

Just use the Simulation constructor and the run command to execute a simulation. The Simulation command must receive all the Meta-Species the user wants to simulate as an argument. For the unicorn example, one could code:

	MySim = Simulation(Unicorn)
	MySim.run()

# Parameter definition

The parameters are defined using the dot notation on the simulation object. For standard parameters, use the dot notation directly, and for plotting parameters, use the '.plot.parameter' model.
Standard parameters can also be configured using a JSON file with the .set_from_json method. As an example, we have the code below:

	MySim.save_data = False
	MySim.plot_data = False
	MySim.duration = 100
	MySim.repetitions = 10
	MySim.plot.xlim = [0,1]
	MySim.plot.ylim = [0, 1e3]

For a list of parameters, check the read_me in the parameter directory. The standard parameters can be found in the get_default_parameters() script in the default_reader() script.

# Units
	
The variable u from the pint python module for unit handling is used to assign units to values in Mobspy. Just add 'u.name_of_the_unit', and MobsPy will handle it. As a code example, we have:

	A(100*u.molar)
	A >> Zero [10/u.nanosecond]

# Compiling

Before simulating, you might want to use the compile method from the Simulation class. The compile function prints all the species, mappings (Meta-Species and species relation), parameters, and reactions. Thus, allowing one to check their model before simulation and executing. 

# Calculations

MobsPy generates an SBML string for each model. The SBML string is passed to basiCO (COPASI in Python), and they handle the calculations. MobsPy returns the data to the user in the Simulation object and a JSON file.

For the rates, MobsPy considers mass action kinetics as default. For different, more complex rates, one can use strings.




	



