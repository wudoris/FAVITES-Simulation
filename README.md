# My Project
Phylogenetic approaches, in which statistical models are fit to genealogies constructed from pathogen
genomes, have recently become an important source of information on the transmission dynamics of diseases. 
The [Pennell lab](https://www.zoology.ubc.ca/person/matthew-pennell) has recently discovered a statistical issue that suggests that in some cases, this
phylogenetic information may be misleading. 

In order to study this potential discrepancy between the actual and reconstructed epidemic, I will utilize FAVITES to simulate viral transmissions.
The viral sequence generated will subsequently be used to reconstruct the epidemic in BEAST2. The examination of the reconstruction and prediction processes will 
shed light on how well and accurate the current methods work.


# FAVITES Simulation

**FAVITES** (FrAmework for VIral Transmission and Evolution Simulation) is a robust modular framework for simulation of viral transmissions and sequence evolution 
([Moshiri *et al*., 2018](https://doi.org/10.1093/bioinformatics/bty921)). By breaking the interaction network down to nodes and edges, the users can easily customize
the simulation process by parameterizing the module classes. 

All the installation information, requirements, usage, file format descriptions, etc., are located in the [FAVITES Wiki](https://github.com/niemasd/FAVITES/wiki).

# Module Implementations

