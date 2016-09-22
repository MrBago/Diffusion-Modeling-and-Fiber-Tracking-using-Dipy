
Here I demonstrate how to use dipy's local tracking. I'll be using 
This example is also availabe as an ipython notebook so that users can download and run the example.

\cite{Chik}
****

``` python
from dipy.data import read_stanford_hardi
img, gtab = read_stanford_hardi()
data = img.get_affine()
affine = img.get_affine()
```


```python
import numpy as np
img.shape, img.affine
mask = np.zeros(img.shape[:-1], bool)
mask[:] = np.random.random(mask.shape) < .001
print mask.sum()
```

```python
"""
from dipy.reconst.dti import TensorModel
dti_model = TensorModel(gtab)
dti_fit = dti_model.fit(img.get_data(), mask)
"""
```


```python
from dipy.reconst.shm import CsaOdfModel

csa_model = CsaOdfModel(gtab, 4)
# csa_fit = csa_model.fit(img.get_data(), mask)
```


```python
from dipy.direction import peaks_from_model
from dipy.data import default_sphere
csa_peaks = peaks_from_model(csa_model, img.get_data(), default_sphere,
                             .8, 30., mask=mask)
```


```python
from dipy.tracking.local.tissue_classifier import ThresholdTissueClassifier
tissue_classifier = ThresholdTissueClassifier(csa_peaks.gfa, .3)
```


```python
from dipy.tracking.utils import seeds_from_mask
seeds = seeds_from_mask(csa_peaks.gfa > .3, affine=affine)
```


```python
from dipy.tracking.local import LocalTracking
streamlines = LocalTracking(csa_peaks, tissue_classifier,
                            seeds, affine=affine, step_size=.5)
```


```python
streamlines = list(streamlines)
print(len(streamlines))
```


```python
sk = seeds_from_mask(mask)[-4:].copy()
```


```python
from dipy.tracking.local import localtracking
reload(localtracking)
import dipy.tracking.local.localtracking
reload(dipy.tracking.local.localtracking)
from dipy.tracking.local.localtracking import LocalTracking
```


```python
seeds = seeds_from_mask(mask, affine=affine)[-4:]
from dipy.tracking.local import LocalTracking
streamlines = LocalTracking(csa_peaks, tissue_classifier,
seeds, affine=affine, step_size=.5)
```
