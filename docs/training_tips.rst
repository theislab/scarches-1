A few tips on training models
===================

trVAE
 - We recommend you to set `recon_loss` = `nb` or `zinb`. These loss functions require access to count data. You need to have raw count data in `adata.raw.X`.
 - If you don't have access to count data and have normalized log-transformed data then set `recon_loss` to  `mse`.
 - trVAE relies on an extra MMD term to force further integration of data sets. There is a parameter called `beta` (default=1) which regulates MMD effect in training. Higher values of `beta` will force extra mixing (might remove biological variation if too big!) while smaller values might result in less mixing (still batch effect). If you set   `beta` = `0` the model reduces to a Vanilla CVAE, but it is better to set 'use_mmd' to 'False' when MMD should not be used.
 - It is important to use highly variable genes for training. We recommend to use at least 2000 HVGs and if you have more complicated datasets, conditions then try  to increase it to 5000 or so to include enough information for the model.
 - Regarding `architecture` always try with the default one ([128,128], `z_dimension`=10) and check the results. If you have more complicated data sets with many datasets and conditions and etc then you can increase the depth ([128,128,128] or [128,128,128,128]).  According to our experiments, small values of `z_dimension` between  10 (default) and 20 are good.

scVI
   - scVI require access to raw count data.
   - scVI already has a default good parameter the only thing you might change is `n_layers` which we suggest increasing to 2 (min) and max 4-5 for more
   complicated datasets.

scANVI
  - It requires access to raw count data.
  - If you have query data the query data should be treated as unlabelled (Unknown) or have the same set of cell-types labels as reference. If you have a new cell-type label that is in the query data but not in reference and you want to use this in the training query you will get an error! We will fix this in future releases.

expiMap
  - The main hyperparameter that affects the quality of integration for the reference training is alpha_kl, the value of which is multiplied by the kl divergence term in the total loss.
  - If the visualized latent space looks like a single blob after the reference training, we recommend to decrease the value of alpha_kl. If the visualized latent space shows bad integration quality, we recommend to increase the value of alpha_kl. The good default value in most cases is alpha_kl = 0.5.
  - The required strength of group lasso regularization (alpha) depends on the number of used GPs and the size of the dataset. For 300–500 GPs, we recommend to use alpha = 0.7 and increase for larger numbers of GPs.
  - If soft mask in the reference training is used (`soft_ext_mask=True` in the model initialization), it is better to start with `alpha_l1=0.5` (higher value means more constraints on how many genes are added to the gene sets) and use `print_stats=True` in the training for monitoring to check the reported "Share of deactivated inactive genes: ​__"  is around 95% (0.95) at the end and stays so at the final 10 epochs of training. If it is much smaller, `alpha_l1` should be increased by a small value (around 0.05), and if it is 100% (1.) then alpha_l1 should be decreased.
  - Using new terms (`n_ext`) in the reference training is not recommended.
