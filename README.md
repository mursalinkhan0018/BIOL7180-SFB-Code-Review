## BIOL7180-SFB-Code-Review

**Title: Sex Differences in Gene Expression and Regulatory Networks across 29 Human Tissues**

*Lopes-Ramos, C.M., Chen, C.Y., Kuijjer, M.L., Paulson, J.N., Sonawane, A.R., Fagny, M., Platig, J., Glass, K., Quackenbush, J., DeMeo, D.L., 2020. Sex Differences in Gene Expression and Regulatory Networks across 29 Human Tissues. Cell Rep. 31. doi:10.1016/j.celrep.2020.107795*

### Introduction
In the code review for the BIOL7180(Scripting for Biologist), I am performing the review of the [Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762) codes. In the paper, the authors focused to understand the molecular basis of sex differences, they evaluated sex-biased gene regulation by constructing sample-specific gene regulatory networks in 29 human healthy tissues using 8,279 whole-genome expression profiles from the Genotype-Tissue Expression (GTEx) project. In the analysis, they identified sex-biased regulatory network structures in each 29 human healthy tissues. According to the authors even though most transcription factors (TFs) are not differentially expressed between males and females, many have sex-biased regulatory targeting patterns. Their systems-based analysis identifies a repertoire of TFs that play important roles in sex-specific architecture of gene regulatory networks.

In terms of code review, the publication is analysis heavy and they frequently mentioned other papers and tools in the methods section instead of explaining each process in detail. To reproduce the networks or analysis one must visit provided links and associated github pages. I am focusing on two gene network generating function of the [netZooM](https://github.com/netZoo/netZooM) used in the paper. They reconstructed sample-specific networks by using PANDA ([Glass et al., 2013](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0064832)) and LIONESS ([Kuijjer et al., 2019](https://www.sciencedirect.com/science/article/pii/S2589004219300872)) as implemented in netZooM 0.1. 

In the paper they used MATLAB based [netZooM](https://github.com/netZoo/netZooM) to create the gene networks to understand sex-dimorphic gene expression in 29 human samples ([Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762)). More precisely they used the `PANDA` (Passing Attributes between Networks for Data Assimilation), which is a message-passing model using multiple sources of information to predict regulatory relationships, and used it to integrate protein-protein interaction, gene expression, and sequence motif data to reconstruct genome-wide, condition-specific regulatory networks ([Glass et al., 2013](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0064832)). 

Although the PANDA is infering connections between genes (or their products) in large numbers of expression samples, aggregate models fail to capture population heterogeneity. Thus they used `LIONESS` (Linear Interpolation to Obtain Network Estimates for Single Samples), a method to reverse engineer sample-specific networks from aggregate networks (PANDA Output-*AgNet*). The `LIONESS` is capable to study changes in network topology across time and to characterize shifts in gene regulation that were not apparent in the expression data ([Lopes-Ramos et al., 2020](https://www.sciencedirect.com/science/article/pii/S2211124720307762)).

The paper itself does not explain the code and analysis in detail but the supplement information [Method S1](https://www.cell.com/cms/10.1016/j.celrep.2020.107795/attachment/46ce2d26-cd81-4ad8-966b-7fcf26e3bd17/mmc1) gave sufficient information to undersatnd the PANDA and LIONESS. The Methods section of the paper has **KEY RESOURCES TABLE**, where I found the link of the **netZooM** which eventually lead me to the `PANDA` github page where I found the [codes](https://github.com/netZoo/netZooM/tree/master/tutorials/panda) for reconstruction of the gene regulatory network in PANDA. In addition, I reviewed the lioness [codes](https://github.com/netZoo/netZooM/blob/master/tutorials/lioness/lioness.pdf) to generate single sample gene regulatory network. 

### Review of netZooM-PANDA
For Codes Click [HERE](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/63335cf4de41b63b78f72a2a3c91164c6888c8af/panda.mlx)

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

**Figure 1. PANDA's Build coexpression network**

5. Based on the Motif information it also calulate the `Build TF binding motif network`. In addition, PANDA can also create protein-protein interaction network in the `Build TF-TF PPI network`. 
6. Finally, after network normalization, it is time for calling `PANDA function` to perform the iterative optimisation procedure.
```
disp('Running PANDA algorithm:');
AgNet = PANDA(RegNet, GeneCoReg, TFCoop, alpha);
```

7. The final result network **AgNet** is a complete bipartite network.

![AgNet](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/a31d5fac568dd63b58cf09ba8c75162aba52cbdc/AgNet-PANDA.PNG)

**Figure 2. PANDA's OutPut "AgNet"**

8. PANDA Final Network Pairs

![PANDA Final Network Pairs](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/7e444fdca57b0f13e313534a3c40d98b2908b19a/Panda-Final-Network-pairs.PNG)

**Figure 3. PANDA's OutPut Final Network Pairs**

#### Comments

- Very well written and documented tools for the network construction.
- Until now I can generate similar results like the original paper using test data (TFs=661 and Genes=1000).
- There was one issue regarding the code run. The code was cut in the pdf. They need to take care of the issue.

![Improper-design markdown](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/9072423e0dc2afafc9bd19a64c51ea8dcbad9f0a/PANDA-code-cut.PNG)
  
**Figure 4 PANDA's Improper-Design markdown**

### Review of netZooM-LIONESS
For Codes Click [HERE](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/63335cf4de41b63b78f72a2a3c91164c6888c8af/lioness.mlx)


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

**Figure 5 LIONESS's Run** 

3. The final result network **PredNet** is a sample-specific network.

4. **My Run Results (test data)**

The number of edges is 661 * 1000 (number of TFs * number of genes)

![PredNet](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/96ab1c9693c761b55eb65611001b0526b07eaa65/PredNet-LIONESS.PNG)

**Figure 6 LIONESS's sample-specific network PredNet**

5. **Original Papers Networks **

The authors put all the networks in the [Grand Gene Regulatory Network Database](https://grand.networkmedicine.org/tissues/). The number of edges is 644 * 30243 (number of TFs * number of genes). 

I downloaded one specific sample network to see the results. TF-by-gene matrix of Adipose - Subcutaneous tissue.

![tissue](https://github.com/mursalinkhan0018/BIOL7180-SFB-Code-Review/blob/3788053d6e769dcd2daf1a7953cf4ee68d260b15/Original%20Paper%20Nerworks.PNG)

**Figure 7 Original Papers Networks (Adipose - Subcutaneous tissue): LIONESS's sample-specific network (PredNet)**

**N.B. I am not sure how to add the TF and gene names in the matrix.**

#### Comments

- Similar to the PANDA it is also very useful and well documented function for the single sample network construction.
- One needs to know the `SET Path` to make sure it is getting the PANDA output.
- I am not sure where to find the info to add TF and gene names in the matrix.

### Concluding Remarks

Overall, the methods, supplement methods, supplement, the linked website and github together can give you the complete codes and understanding how to reproduce or replicate the results. However, the original paper and methods are not fully well explained. I think in the analysis paper it should be very common to give external and github links. In case of the [netZooM](https://github.com/netZoo/netZooM), it is well documented tools for the expression network, the motif network, and the ppi network. I think one needs to read the following reference papers to understand the functions completely [1-2].

References

1. Glass, Kimberly, et al. "Passing messages between biological networks to refine predicted interactions." PloS one 8.5 (2013): e64832.
2. Kuijjer, Marieke Lydia, et al. "Estimating sample-specific regulatory networks." iScience 14 (2019): 226-240.


