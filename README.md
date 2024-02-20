<p align="center"> Geomapping Pipeline </p>
<p align="center"> <img width="616" src="./docs/Figure1_Pipeline.png" width=50% height=50%/> </p>
<!--asldfkj-->
---
![GitHub Status](https://github.com/github/docs/actions/workflows/main.yml/badge.svg)

**SDOH (Social Determinants of Health)** is XXX (see [papers](#citations) for details and citations).

<!--**SHAP (SHapley Additive exPlanations)** is a unified approach to explain the output of any machine learning model. SHAP connects game theory with local explanations, uniting several previous methods [1-7] and representing the only possible consistent and locally accurate additive feature attribution method based on expectations (see our [papers](#citations) for details and citations).-->

## Install Required Packages

All the required packages can be installed as below:
<pre>
pip install package (package: )
<i>or</i>
conda install -c conda-forge shap
</pre>

## Check 

While SHAP can explain the output of any machine learning model, we have developed a high-speed exact algorithm for tree ensemble methods (see our [Nature MI paper](https://rdcu.be/b0z70)). Fast C++ implementations are supported for *XGBoost*, *LightGBM*, *CatBoost*, *scikit-learn* and *pyspark* tree models:

```python
import xgboost
import shap

# train an XGBoost model
X, y = shap.datasets.california()
model = xgboost.XGBRegressor().fit(X, y)

# explain the model's predictions using SHAP
# (same syntax works for LightGBM, CatBoost, scikit-learn, transformers, Spark, etc.)
explainer = shap.Explainer(model)
shap_values = explainer(X)

# visualize the first prediction's explanation
shap.plots.waterfall(shap_values[0])
```



```python
# visualize the first prediction's explanation with a force plot
shap.plots.force(shap_values[0])
```

<p align="center">
  <img width="811" src="./docs/artwork/california_instance.png" />
</p>

If we take many force plot explanations such as the one shown above, rotate them 90 degrees, and then stack them horizontally, we can see explanations for an entire dataset (in the notebook this plot is interactive):

```python
# visualize all the training set predictions
shap.plots.force(shap_values[:500])
```

<p align="center">
  <img width="811" src="./docs/artwork/california_dataset.png" />
</p>

To understand how a single feature effects the output of the model we can plot the SHAP value of that feature vs. the value of the feature for all the examples in a dataset. Since SHAP values represent a feature's responsibility for a change in the model output, the plot below represents the change in predicted house price as the latitude changes. Vertical dispersion at a single value of latitude represents interaction effects with other features. To help reveal these interactions we can color by another feature. If we pass the whole explanation tensor to the `color` argument the scatter plot will pick the best feature to color by. In this case it picks longitude.

```python
# create a dependence scatter plot to show the effect of a single feature across the whole dataset
shap.plots.scatter(shap_values[:, "Latitude"], color=shap_values)
```

## Sample notebooks

The notebooks below demonstrate different use cases for SHAP. Look inside the notebooks directory of the repository if you want to try playing with the original notebooks yourself.

## Citations

The algorithms and visualizations used in this package came primarily out of research in [Su-In Lee's lab](https://suinlee.cs.washington.edu) at the University of Washington, and Microsoft Research. If you use SHAP in your research we would appreciate a citation to the appropriate paper(s):

- For general use of SHAP you can read/cite our [NeurIPS paper](http://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) ([bibtex](https://raw.githubusercontent.com/shap/shap/master/docs/references/shap_nips.bib)).
- For TreeExplainer you can read/cite our [Nature Machine Intelligence paper](https://www.nature.com/articles/s42256-019-0138-9) ([bibtex](https://raw.githubusercontent.com/shap/shap/master/docs/references/tree_explainer.bib); [free access](https://rdcu.be/b0z70)).
- For GPUTreeExplainer you can read/cite [this article](https://arxiv.org/abs/2010.13972).
- For `force_plot` visualizations and medical applications you can read/cite our [Nature Biomedical Engineering paper](https://www.nature.com/articles/s41551-018-0304-0) ([bibtex](https://raw.githubusercontent.com/shap/shap/master/docs/references/nature_bme.bib); [free access](https://rdcu.be/baVbR)).

<img height="1" width="1" style="display:none" src="https://www.facebook.com/tr?id=189147091855991&ev=PageView&noscript=1" />
