If you have a dataset with both EPICv1 and EPICv2 please combine them in one batch.

We recommend to add a covariate for array.

In the `config` file you can add `epic:epic2` as methylation array.

Meffil can normalise these data in one batch:

```
qc.objects <- meffil.qc(samplesheet, featureset="epic:epic2", verbose=T)
```