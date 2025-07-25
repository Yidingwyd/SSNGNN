# SSNGNN
Solid Solution Nested Graph Neural Network (without edge features in the compositional graph)

**Note:** This package is the version without edge features in the compositional graph.  
          For the version with edge features in the compositional graph, e.g., study of the chemical short-range order (SRO) in solid solutions, please refer to the second version [SSNGNN_v2](https://github.com/Yidingwyd/SSNGNN_v2).  
          Contact with me if there is any question about SSNGNN: yidingwyd@163.com  

# Contents  
* [Brief review](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#brief-review)  
* [How to cite](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#how-to-cite)  
* [Prerequisites](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#prerequisites)  
* [Usage](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#usage)
  * [Input of the SSNGNN](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#input-of-the-ssngnn)
  * [Train a SSNGNN model](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#train-a-ssngnn-model)
  * [Predict using a trained SSNGNN model](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#predict-using-a-trained-ssngnn-model)
  * [Data](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#data)
* [Acknowledgement](https://github.com/Yidingwyd/SSNGNN?tab=readme-ov-file#acknowledgement)  

# Brief review
SSNGNN provides a unified end-to-end representation learning framework for solid solution materials.   
In simple terms, SSNGNN consists of two layers in a nested graph representation: an inner compositional graph and an outer structural graph.  
   * The inner compositional graph captures the compositional information of the solid solution by representing different elements at each lattice site.  
   * The outer structural graph models the overall crystal structure, aggregating features from the compositional graphs based on the spatial arrangement of lattice sites.  

# How to cite
Our paper is in submission now.

# Prerequisites
This Python package requires: pytorch, torch_scatter, scikit-learn, pymatgen, pandas, numpy, json.

# Usage
## Input of the SSNGNN  
Two `.json` files are required to be input into the SSNGNN:  
* Input dataset.  
* Atomic embeddings.

As an example, one can refer to the [ICSD - CPA dataset](https://github.com/Yidingwyd/SSNGNN/blob/main/Kfold/cpa/cpa_formation_energy_per_atom.json), of which structures are from [ICSD](https://icsd.products.fiz-karlsruhe.de/) with formation energies calculated by Coherent Potential Approximation (CPA).  
The **input dataset** of the SSNGNN should be saved as a python dictionary (named as `dataset dictionary`) in a `.json` file. 
* `dataset dictionary`:  
\- key - ID of each sample;  
\-  valule - `sample dictionary`.
* `sample dictionary`:  
\- keys `a`, `b`, `c`, `alpha`, `beta`, and `gamma` - the lattice parameters that describe the unit cell;  
\- key `comp` - `site dictionary`;  
\- key `target` - target (property) of the sample.  
* `site dictionary`:  
\- key - the relative positions of lattice sites, enclosed in square brackets [];  
\- value - the chemical compositions at those sites, which should be readable by pymatgen.

A sample representing a BCC solid solution is shown below:  
![An example for a BCC sample](https://github.com/Yidingwyd/SSNGNN/blob/main/Kfold/cpa/fig1.png)  
If the solid solution exhibits a sublattice structure, where the composition varies across different lattice sites, the values in the `site dictionary` can also differ accordingly. A sample representing a perovskite solid solution is shown below:  
![An example for a perovskite sample](https://github.com/Yidingwyd/SSNGNN/blob/main/Kfold/perovskite_band_gap/fig2.png)  
  
If you have solid solution cifs and a dataset csv like that in [cif_to_data](https://github.com/Yidingwyd/SSNGNN/tree/main/data/cif_to_data), [cif_to_data.py](https://github.com/Yidingwyd/SSNGNN/blob/main/data/cif_to_data/cif_to_data.py) may help you to generate a input dataset file.  
  
Another input file is the **atomic embeddings**, i.e., features in the nodes of the compositional graphs. We used [one-hot features](https://github.com/Yidingwyd/SSNGNN/blob/main/data/perov_onehot_embedding.json) and [SSPM features](https://github.com/Yidingwyd/SSNGNN/blob/main/data/SSPM.json) [(Y.Wang, et al. 2024)](https://github.com/Yidingwyd/SSPM) in perovskite and other datasets, respectively. Users may try other atomic embeddings, which should also be saved as a python dictionary in a `.json` file.  

Based on the input files, the program will automatically generate PyTorch tensors in a style of nested graph representation.  
## Train a SSNGNN model  
You can train a SSNGNN model by:  
```
python main_new.py --task regression --train_data train_set_path --val_data val_set_path --embedding atomic_embedding_path --savepath model_save_path  
```
The model will be saved after every epoch, and the best model which shows the best performance on the validation set will be saved as `best.pth.tar` in the `model_save_path`.  
The input parameters of the model are summarized in the following table：  
![Table 1](https://github.com/Yidingwyd/SSNGNN/blob/main/table1.png)  
## Predict using a trained SSNGNN model
You can predict by:  
```
python predict.py --task regression  --test_data test_set_path --embedding atomic_embedding_path --modelpath model_path --savepath predictioin_results_save_path_and_name_ended_by_xlsx  
```
It should be noted that if any of the hyperparameters listed in the above table are modified during training, the same settings must be used during prediction. Meanwhile, the atomic embeddings must remain consistent with that used during training.  
  
The order of samples in the generated `.xlsx` file is consistent with that in the test set. The "formula" column is provided for reference only and does not represent the actual chemical formula.  
## Data  
To facilitate reproducibility of our work, all datasets used in our paper have been converted into `.json` files in [Kfold](https://github.com/Yidingwyd/SSNGNN/tree/main/Kfold). Please cite the relevant papers as requested by the dataset authors.  
# Acknowledgement  
Codes of the SSNGNN are developed based on [CGCNN](https://github.com/txie-93/cgcnn) and [Roost](https://github.com/CompRhys/roost). We strongly recommend to cite their works.  

