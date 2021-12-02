# My Project
Phylogenetic approaches, in which statistical models are fit to genealogies constructed from pathogen
genomes, have recently become an important source of information on the transmission dynamics of diseases. 
Recently, multiple methods have been developed to infer properties of transmission networks from molecular data, such as BEAST2.
The [Pennell lab](https://www.zoology.ubc.ca/person/matthew-pennell) has recently discovered a statistical issue that suggests that in some cases, this
phylogenetic information may be misleading. 

In order to study the promise and limitation of BEAST2, and any potential discrepancy between the actual and reconstructed epidemic, I will utilize FAVITES to simulate viral transmissions with the same substitution rates used in [this study](https://doi.org/10.1093/molbev/msab149). The viral sequence generated will subsequently be used to reconstruct the epidemic in BEAST2. The examination of the reconstruction and prediction processes will shed light on how well and accurate the current methods work. More broadly, the effectiveness of these methods in helping facilitate public health interpretations is the subject of ongoing clinical and theoretical research, including mine.


# Transmission Network and FAVITES Simulation

It is widely accepted that social and sexual networks drive the spread of infectious diseases. Therefore, reconstructing the transmission histories using molecular data sampled in patients could provide crucial information to enhance intervention. The nature of a transmission network is a contact network, a graph with nodes representing the individuals and edges representing any type of contact.

**FAVITES** (FrAmework for VIral Transmission and Evolution Simulation) is a robust modular framework for simulation of viral transmissions and sequence evolution 
([Moshiri *et al*., 2018](https://doi.org/10.1093/bioinformatics/bty921)). By breaking the interaction network down to nodes and edges, the users can easily customize the simulation process by parameterizing the module implementations. In the statistical sense, each sub-model created by one module implementation can be integrated into the complex composite generative model. 

![This is an image](https://oup.silverchair-cdn.com/oup/backfile/Content_public/Journal/bioinformatics/35/11/10.1093_bioinformatics_bty921/3/bty921f1.jpeg?Expires=1641417451&Signature=ioS7Jj9rgeLAyuy4Dvcv8YvilQ4rdTA~e5HCszcA1qTW2ryrFEacp4GuSFaHef0q-UhwsyLJUua74xijyzKXM2iYECFG8cTiEWhix8Qk28SAMaHxENwdP5etwK9ExfE2dviP6KGgaUv2LyNbIq2KVOJTRrRsUNiZdfzz8ZNYAumYCfw63iYlZVt3KNBX4a1kOx2hj62XvUfX94wbLilI-6YrvGPmmKbSIutMvoYTyBXqJxOO1UOxOVdkIQq5lY9r7xDK9YLXDglT2XjawTEflLHG7tN2PogiaSy3WbtacCHOLyvIaDHgMHcGO7xR-VeABqx52rz9j-0p7iYdQpbTNQ__&Key-Pair-Id=APKAIE5G5CRDK6RD3PGA)

All the installation information, requirements, usage, file format descriptions, etc., are located in the [FAVITES Wiki](https://github.com/niemasd/FAVITES/wiki).

# Module Implementations
In this section, we will discuss the module implementations important for simulating the epidemic.

1. *ContactNetworkGenerator*
   - Creates an edge list representung the input contact network (either by reading it from file or generating it)
   - Uses in conjunction with *ContactNetworkEdge* and *ContactNetworkNode*
   - **Barabasi-Albert Model** used for implementation
     - A graph of n nodes is grown by attaching new nodes each with m edges that are preferentially attached to existing nodes with high degree
     - Config parameters: **num_cn_nodes** and **num_edges_from_new**


2. *End Criteria*
   - Determines whether or not the simulation process should terminate at any given moment
   - **FirstTimeTransmissions** used for implementation
     - Users can specify both an end time and a number of transmissions as ending criteria, and the first to be reached will end the simulation
     - Config parameters: **end_time** and **end_transmissions**
   - Other implementations such as GEMF are not used here to avoid complexity 

3. *NodeEvolution*
   - Evolves the phylogenetic trees of the viruses within a specified node until a specified time
   - **VirusTreeSimulator** used for implementation
     - Evolves viral phylogenetic trees under a coalescent model with various effective population growth models: constant, exponental or logistic
     - **vts_model**: sets the desired viral population growth model to use (constant, exponential, logistic)
     - **vts_n0**: sets the desired effective population size at time zero, 1 is utilized if you assume a single viral was transmitted
     - **vts_growthRate**: sets the desired effective population size growth rate (only used in exponential and logistic models)
     - **vts_max_attempts**: sets the maximum number of attempts to coalescence a single tree (e.g. 100) before FAVITES kills VirusTreeSimulator

4. *SequenceEvolution*
   - Evolves the sequences of the viruses within a specified node until a specified time
   - Lots of implementations can be appropriate here, to be consistent with previous studies, the substitution rates for the HIV transmission were used as input here
 
5. *TransmissionNodeSample*
   - Chooses two nodes to be involved in a given transmission event
   - **RandomSingleTransmission** is used here to avoid complexity 

Besides parameters specific to the epidemic itself, the real-life sampling effort can also be designed via the following modules.

6. **NodeAvailability**
   - Subsamples the nodes in the Contact Network (to simulate imperfect epidemiological efforts)
   
7. **NumBranchSample**
   - Chooses the number of viral phylogeny branches to be sampled during a single sampling (i.e. sequencing) event of a given individual in the ContactNetwork
   
8. **Sequencing**
   - Simulates sequencing imperfections, such as…
     - Sequence subsampling per individual
     - Sequencing error
     - Post-processing
     - Consensus (ambiguity, etc.)

9. **TimeSample**
   - Chooses times at which a given individual in the ContactNetwork was sampled (i.e. sequenced)

Other module implementations are also required to be specified in the config file to provide coherent instructions to the simulation. However, only the ones mentioned above have crucial biological meaning for my project (and were thus discussed in detail). More information on the complete list of modules can be found in the the [FAVITES Wiki](https://github.com/niemasd/FAVITES/wiki).

# Generating the docker image
Following instructions provided above and referring to the "final_config.json" file in the main branch of this repository, you should now have you config file ready to run FAVITES. If you installed FAVITES [using the Docker image](https://github.com/niemasd/FAVITES/wiki/Installation#using-docker-recommended) (recommended), you can run FAVITES using `run_favites_docker.py` in your terminal:

An example would be:

 `run_favites_docker.py -c final_config.json`

- Sometimes you might see an error messages such as:

`Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?`
`RuntimeError: docker images command failed`

If you see this, make sure you have your Docker Desktop open already.

- Warning messages could also occur but you don't need to worry about it too much:

`WARNING: The requested image's platform (linux/amd64) does not match the detected host plastform (linux/arm64/v8) and no specific platform was requested`


# Managing the files
For convenience and also for terminal too get access to all the files included in this project, I stored all my files in one folder. I would recommend doing this.

Sometimes terminal can have trouble looking for the files you want, so it would be helpful if you change the directory to the file you made before running FAVITES.

This could be done by: (also in terminal)

`cd /User/doriswu`

# Output files
After Docker finishes running your FAVITES simulation, it will provide you with information such as the time taken for running and also the output size.
The output files contain everything we want from the simulation: the sequence data, the transmission network and the time trees.
There are independent folders for `error_free_files` and `error_prone_files`.

For more information on the output file structure can be found [here](https://github.com/niemasd/FAVITES/wiki/Output-Folder-Structure).

To open the sequence data file (fasta.gzip) which will be analyzed in BEAST2, run command line `gunzip` in terminal.

