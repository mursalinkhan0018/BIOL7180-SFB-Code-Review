## BIOL7180-SFB-Code-Review [Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762)

### Introduction
In the code review for the scripting for biologist I am performing to review codes for the [Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762).Overall, the publication is analysis heavy and they mention other papers in the methods and material section instead of explaining each process, which is understandable for analytical publications. To reproduce the networks or analysis one must visit provided links and associated github pages. I am focusing on two gene network generating function of the [netZooM](https://github.com/netZoo/netZooM) used in the paper. They reconstructed sample-specific networks by using PANDA ([Glass et al., 2013](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0064832)) and LIONESS ([Kuijjer et al., 2019](https://www.sciencedirect.com/science/article/pii/S2589004219300872)) as implemented in netZooM 0.1. 

In the paper they used MATLAB based [netZooM](https://github.com/netZoo/netZooM) to create the gene networks to understand sex-dimorphic gene expression in 29 human samples ([Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762)). More precisely they used the `PANDA` (Passing Attributes between Networks for Data Assimilation), which is a message-passing model using multiple sources of information to predict regulatory relationships, and used it to integrate protein-protein interaction, gene expression, and sequence motif data to reconstruct genome-wide, condition-specific regulatory networks ([Glass et al., 2013](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0064832)). 

Although the PANDA is infering connections between genes (or their products) in large numbers of expression samples, aggregate models fail to capture population heterogeneity. Thus they used `LIONESS` (Linear Interpolation to Obtain Network Estimates for Single Samples), a method to reverse engineer sample-specific networks from aggregate networks (PANDA Output). The `LIONESS` is capable to study changes in network topology across time and to characterize shifts in gene regulation that were not apparent in the expression data ([Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762)).

The paper itself does not explain the code and analysis in detail but the supplement information [Method S1](https://www.cell.com/cms/10.1016/j.celrep.2020.107795/attachment/46ce2d26-cd81-4ad8-966b-7fcf26e3bd17/mmc1) gave sufficient information to undersatnd the PANDA and LIONESS. The Methods section of the paper has **KEY RESOURCES TABLE**, where I found the link of the **netZooM** which eventually lead me to the `PANDA` github page where I found the [codes](https://github.com/netZoo/netZooM/tree/master/tutorials/panda) for reconstruction of the gene regulatory network in PANDA. In addition, I reviewed the lioness [codes](https://github.com/netZoo/netZooM/blob/master/tutorials/lioness/lioness.pdf) to generate single sample gene regulatory network. 

### Review of netZooM-PANDA

1. The netZooM is a MATLAB based software package to create networks from gene expression data. I successfully clone the package in my `Local Drive` by 

```
git clone https://github.com/netZoo/netZooM.git
```

Then add netZooM to your MATLAB path
```
addpath(genpath('path/to/netZooM'))
```

2. I used the test data set of the PANDA to run [codes](https://github.com/netZoo/netZooM/tree/master/tutorials/panda) because the data of the original paper [Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762) needs extensive filtering as they mentioned in the paper. But, they provide enough external links to reproduce the analysis. In the external links, the code is very well explained if anyone has little knowledge in the MATLAB. The input files looks like

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
![Build coexpression network](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/9072423e0dc2afafc9bd19a64c51ea8dcbad9f0a/PANDA-code-Expression-data.PNG)

5. Based on the Motif information it also calulate the `Build TF binding motif network`. In addition, PANDA can also create protein-protein interaction network in the `Build TF-TF PPI network`. 
6. Finally, after network normalization, it is time for calling `PANDA function` to perform the iterative optimisation procedure.
```
disp('Running PANDA algorithm:');
AgNet = PANDA(RegNet, GeneCoReg, TFCoop, alpha);
```

The final result network **AgNet** is a complete bipartite network.


![AgNet](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/a31d5fac568dd63b58cf09ba8c75162aba52cbdc/AgNet-PANDA.PNG)


#### Comments
- Very well written and documented tools for the network construction.
- There was one issue regarding the code run. The code was cut in the pdf. They need to take care of the issue.

![Improper-design markdown](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/9072423e0dc2afafc9bd19a64c51ea8dcbad9f0a/PANDA-code-cut.PNG)
  
### Review of netZooM-LIONESS

The `LIONESS` function generate single sample gene regulatory networks unlike `PANDA` used all the samples. It is also the part of the netZooM package.  

#### Using LIONESS to infer single sample gene regulatory networks

1. `LIONESS` use the outout data from PANDA (*AgNet(input for LIONESS)*). But, it can use any other similarly formated data as well. Like `PANDA`, **LIONESS** uses the expression network, the motif network, and the ppi network. N.B. In the [lioness code](https://github.com/netZoo/netZooM/blob/master/tutorials/lioness/lioness.pdf) they used the output of the `PANDA`

```
panda_file = 'panda2.test.mat'; % This test network has been transposed
% to test savePairs, so we need to tranpose it back
load('test_data/panda.test.mat');
AgNet      = AgNet';
save('panda2.test.mat','AgNet');
alpha      = 0.1;
START      = 1;  % sample-of-interest starting from this index
END        = 1;  % sample-of-interest ending to this index; use -1 to end at the last sample
ascii_out  = 0;  % set to 1 if you prefer text output file instead of MAT-file
save_dir   = 'C:/Users/Mursalin/Desktop/Scripting_for_Biologist/Projects/netZooM/tests/test_data';
lib_path   = 'C:/Users/Mursalin/Desktop/Scripting_for_Biologist/Projects/netZooM';
```

2. The code call `LIONESS` using the `lioness_run` function. LIONESS does not output an enviroment variable but saves all the START-END+1 networks in files.

```
lioness_run(exp_file, motif_file, ppi_file, panda_file, save_dir, START, END, alpha, ascii_out, lib_path);
```
N.B.: Make sure the paths are correct otherwise it will give ERROR.

![LIONESS](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/2ff3e09d53e7d4916997bd0153028cb5c2f50255/LIONESS-Run.PNG)

3. The final result network **PredNet** is a sample-specific network.

![PredNet](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/96ab1c9693c761b55eb65611001b0526b07eaa65/PredNet-LIONESS.PNG)

#### Comments
- Similar to the PANDA it is also very useful and well documented function for the single sample network construction.
- One needs to know the `SET Path` to make sure it is getting the PANDA output. 

### Concluding Remarks

The [netZooM](https://github.com/netZoo/netZooM) is well documented and reproducible tools for the network analysis of the expression network, the motif network, and the ppi network. I think one needs to read the following reference papers to understand the function completely.

References

1. Glass, Kimberly, et al. "Passing messages between biological networks to refine predicted interactions." PloS one 8.5 (2013): e64832.
2. Kuijjer, Marieke Lydia, et al. "Estimating sample-specific regulatory networks." iScience 14 (2019): 226-240.


