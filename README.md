## BIOL7180-SFB-Code-Review ![Camila et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762)

### Introduction
In the code review for the scripting for biologist I am performing to review codes for the ![Camila et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762). In the paper they used MATLAB based ![netZooM](https://github.com/netZoo/netZooM) to create the gene networks. More specifically they used the `PANDA` (Passing Attributes between Networks for Data Assimilation), which is a message-passing model using multiple sources of information to predict regulatory relationships, and used it to integrate protein-protein interaction, gene expression, and sequence motif data to reconstruct genome-wide, condition-specific regulatory networks in yeast as a model ![Glass et al., 2013](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0064832).

From the link of the paper's **KEY RESOURCES TABLE** I found the link of the **netZooM** which eventually lead me to the `PANDA` github page where I found the ![codes](https://github.com/netZoo/netZooM/tree/master/tutorials/panda) for reconstruction of the gene regulatory network in PANDA. In addition, I reviewed the lioness ![codes](https://github.com/netZoo/netZooM/blob/master/tutorials/lioness/lioness.pdf) to generate single sample gene regulatory network. 

### Review of netZooM-PANDA

1. The netZooM is a MATLAB based software package to create networks from gene expression data. I successfully clone the package in my `Local Drive` by 

```
git clone https://github.com/netZoo/netZooM.git
```

Then add netZooM to your MATLAB path
```
addpath(genpath('path/to/netZooM'))
```

2. I used the test data set of the PANDA to run ![codes](https://github.com/netZoo/netZooM/tree/master/tutorials/panda). The code is very well explained if anyone has little knowledge in the MATLAB. The input files looks like

```
% Set Program Parameters
exp_file   = 'tests/test_data/expression.txt';
motif_file = 'tests/test_data/motifTest.txt';
ppi_file   = 'tests/test_data/ppi.txt';
panda_out  = '';  % optional, leave empty if file output is not required
lib_path   = '../netZooM';  % path to the folder of PANDA source code```
```

3. Exploring the data to understand what is necessary for the PANDA network generation. 
4. The codes read the normalized expression data and compute the coexpression network by using 

```
disp('Computing coexpression network:');
GeneCoReg = Coexpression(Exp);
```
The visualization of the network will use another tools not the PANDA.
5. Based on the Motif information it also calulate the `Build TF binding motif network`. In addition, PANDA can also create protein-protein interaction network in the `Build TF-TF PPI network`. 




  


