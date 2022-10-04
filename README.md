# Search for pharmacophores based on bioactive molecules in key proteins for Alzheimer's disease

Using a workflow implemented with the [Knime](https://www-knime-com.translate.goog/?_x_tr_sl=auto&_x_tr_tl=es&_x_tr_hl=es-419)
 tool (open source software used in data science) and bash code, we started with a dataset of key proteins associated with AD, for each of them we identified the bioactive molecules (EC50, IC50 and Ki) from the database [ChEMBL](https://www.ebi.ac.uk/chembl/). Each set of molecules were filtered according to Lipinski's rules and a similarity matrix was calculated using the Morgan fingerprint and Tanimoto similarity coefficient. Subsequently, they were classified by hierarchical clustering, thus finding clusters of bioactive molecules against each target, but different from each other. Finally, for each cluster, pharmacophore hypotheses were generated with Phase software, selecting the best one with respect to the phasehyposcore, resulting in a set of pharmacophores.

## Requirements
- Knime version 4.6, open source software for data analysis.
- Install rename to run script 2 and 3 in bash "sudo apt install rename", if using conda "conda install -c bioconda rename".
 
This workflow is composed of 2 parts, the first corresponds to a flow with Knime that is subdivided into 4, which can be seen in the figure and the second part corresponds to 3 codes implemented with bash. 

 ![Workflow KNIME](figures/fig_1.png)

 
  #### 1. Input file
The input file is in csv format and presents a dataset with active molecules and in this case its characteristics include: inhibition constant (ec50, ic50 and ki), id_CHEMBL, standard inchi/inchi-key and SMILES.

 ![CSV-knime](figures/fig_2.png)

In addition they are separated by each type of inhibition that they present as already mentioned ec50, ic50 and Ki.

 ![nodo-standar type](figures/fig_3.png)
  
#### 2. Molecular filtering: ADME and lead-likness criteria

The next step is a molecular filtering applying the Lipinski rule of 5 (LogP <=5, molecular weight <=500 gr/mol, hydrogen bond acceptors <= 10 and hydrogen bond donors <=5). In this case, molecular weight <=600 gr/mol is considered and these characteristics are calculated for each of the molecules and the compounds that respect 3 or 4 of the characteristics corresponding to molecular properties that have pharmacokinetic importance in the human body are selected.

##### Step 1: Calculate MW, HBD, HBA, and LogP
With the canonical format obtained from RDKit, the salts that may be present in the molecules are removed and the properties of the Lipinski rules are calculated.

##### Step 2:Filter dataset by Lipinski's rule of five
With the properties obtained from the previous step for each of the molecules, a filter is applied for each property (MW, HBD, HBA, LogP) and a Boolean value is assigned (1 complies and 0 does not comply), then these values are added for each molecule and all molecules with a value of 3 or 4 are selected, thus considering only those molecules that comply with 3 or 4 of the pharmacokinetic characteristics evaluated.

   
#### 3. Get compound clustering


##### Step 3: Cluster dataset with hierarchical clustering algorithm
The molecules are grouped according to the chemical structural similarity between them and thus find groups that share a common scaffold. These molecules are identified with fingerprints in this case was carried out by Morgan FingerPrint and in the case of similarity can be described by the Tanimoto coefficient, which varies from zero to one, where values close to zero represent a low similarity and values close to 1 a high similarity.

(figure fingerprint and tanimoto)

The hierarchical clustering algorithm is then used to identify groups that present similar compounds, thus generating representative clusters. 

(figure dendogram clusters)

##### Step 4: Get significant cluster 
