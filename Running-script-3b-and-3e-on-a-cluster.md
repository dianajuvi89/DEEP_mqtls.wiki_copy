### Optional: Running on the cluster
#### Script 3b
We can run the `03b-methylation_adjustment1.sh` and `03e-methylation_adjustment2.sh` in parallel on a cluster. In the `config` file you can specify how many chunks to break the entire analysis into, e.g. 

    meth_chunks="100"

will split the ~850k probes into 100 chunks of ~8.5k each. If you specify a number between 1 and 100 as an argument to this script, e.g.:

    ./03b-methylation_adjustment1.sh 1

then the script will run only the 1st chunk of the entire dataset, i.e. approximately, probes 1-8500.

To utilise this on a cluster requires three steps:

1. Creating a submission script that works for your cluster

2. Check whether your jobs are completed

3. Aggregating the individual results from each chunk into a single file at the end.

An example of how a submission script, e.g. `submit_03b.sh` for our cluster would be written is as follows:


```bash
#!/bin/bash
#SBATCH --job-name=meth_03b
#SBATCH --nodes=1
#SBATCH --mem=25G
#SBATCH --ntasks=1
#SBATCH --time=0-50:00:00
#SBATCH --array=1-100
#SBATCH --output=job_reports/slurm03b-%A_%a.out

set -e

echo "Running on ${HOSTNAME}"

cd /path/to/DEPP_mqtls/
./03b-methylation_adjustment1.sh ${SLURM_ARRAY_TASK_ID}

```

then to run:
```
sbatch submit_03b.sh
```
On our cluster this creates a batch of 100 jobs, each with the `${SLURM_ARRAY_TASK_ID}` variable set to a value between 1 and 100. 

You can check whether your jobs are completed by grepping `Successfully` from the log files in (`results/03/logs_b`):
```
cd results/03/logs_b
grep "Successfully adjusting covariates" log.txt* | wc -l
```
100 should be returned if 100 batch jobs are successful.

You can check which job is failed by running 
```
find . -type f -exec grep -L "Successfully adjusting covariates" {} +
```
and then resubmit the failed job by running 
```
source config
cd $home_directory
./03b-methylation_adjustment1.sh ${failed_task_id}
```
After these jobs have completed, assuming there have been no errors, there will be multiple `.RData` files. You can check this by going to the DEEP_mQTLs directory and type

```
source config
cd $home_directory
ls ./processed_data/methylation_data/untransformed_methylation_adjusted.[0-9]* |wc -l
ls ./processed_data/methylation_data/untransformed_methylation_adjusted.Male.chrX.[0-9]* |wc -l
ls ./processed_data/methylation_data/untransformed_methylation_adjusted.Male.chrY.[0-9]* |wc -l
ls ./processed_data/methylation_data/untransformed_methylation_adjusted.Female.chrX.[0-9]* |wc -l
ls ./processed_data/methylation_data/transformed_methylation_adjusted.[0-9]* |wc -l
ls ./processed_data/methylation_data/transformed_methylation_adjusted.Male.chrX.[0-9]* |wc -l
ls ./processed_data/methylation_data/transformed_methylation_adjusted.Male.chrY.[0-9]* |wc -l
ls ./processed_data/methylation_data/transformed_methylation_adjusted.Female.chrX.[0-9]* |wc -l
```

Please note that `Male.chrX`, `Male.chrY`, or `Female.chrX` RData files won't be generated if you have a single sex dataset. Please also note that we would expect 100 `untransformed_methylation_adjusted.[0-9]*` and 100 `transformed_methylation_adjusted.[0-9]*` RData files. 
If any DNAm subsets only have chrX or chrY CpGs, `(un)transformed_methylation_adjusted.[0-9]*Rdata` (including autosomal CpGs) won’t be generated but `(un)transformed_methylation_adjusted.Female.chrX.[0-9]*Rdata; (un)transformed_methylation_adjusted.Male.chrX.[0-9]*Rdata, and (un)transformed_methylation_adjusted.Male.chrY.[0-9]*Rdata` (including sex chr CpGs) will be generated. This situation is more likely to occur with 450K data—fewer CpGs in each DNAm subset lead to a higher probability of all-sex chr CpGs.
 
The next step is to combine the `RData` files in a single file. An example of how a submission script, e.g. `godmc_03b_aggregate.sh` for our cluster would be written is as follows:

```bash
#!/bin/bash -l
#SBATCH --job-name=meth_03b_aggregate
#SBATCH --partition gpu,cpu
#SBATCH --mem=25GB
#SBATCH --ntasks=1
#SBATCH --time=6:0:0
#SBATCH --output=job_reports/meth_03b_aggregate_%j


set -e
source ./config

bash ./resources/methylation/aggregate_adjustment1.sh

count0=`ls ${transformed_methylation_adjusted}* | grep ${transformed_methylation_adjusted}.Female.chrX | wc -l`
count1=`ls ${transformed_methylation_adjusted}* | grep ${transformed_methylation_adjusted}.Male.chrX | wc -l`
count2=`ls ${transformed_methylation_adjusted}* | grep ${transformed_methylation_adjusted}.Male.chrY |wc -l`

if [ ${count0} -gt 0 ]
then
    bash ./resources/methylation/aggregate_adjustment1.sh Female.chrX
else
    echo "No chrX CpGs from female samples"
fi

if [ ${count1} -gt 0 ]
then
    bash ./resources/methylation/aggregate_adjustment1.sh Male.chrX
else
    echo "No chrX CpGs from male samples"
fi

if [ ${count2} -gt 0 ]
then
    bash ./resources/methylation/aggregate_adjustment1.sh Male.chrY
else
    echo "No chrY CpGs from male samples"
fi

```

Four log files will be generated to check the aggregation of sex chromosome CpGs ```log.txt_Female.chrX_aggregation, log.txt_Male.chrX_aggregation, log.txt_Male.chrY_aggregation``` and autosomal CpGs ```log.txt__aggregation```, except if the cohort has female or male samples only. To ensure the aggregation job ran successfully, "The number of CpGs before and after adjustment are consistent" should be printed twice in each log file because each job run aggregate of transformed and untransformed methylation data.

```
grep "The number of CpGs before and after adjustment are consistent" ./results/03/logs_b/log.txt_Female.chrX_aggregation| wc -l
grep "The number of CpGs before and after adjustment are consistent" ./results/03/logs_b/log.txt_Male.chrX_aggregation| wc -l
grep "The number of CpGs before and after adjustment are consistent" ./results/03/logs_b/log.txt_Male.chrY_aggregation | wc -l
grep "The number of CpGs before and after adjustment are consistent" ./results/03/logs_b/log.txt__aggregation | wc -l
```
and eight aggregated RData should be generated in path `./processed_data/methylation_data/`
```
untransformed_methylation_adjusted.Female.chrX.RData
untransformed_methylation_adjusted.Male.chrX.RData
untransformed_methylation_adjusted.Male.chrY.RData
untransformed_methylation_adjusted.RData
transformed_methylation_adjusted.Female.chrX.RData
transformed_methylation_adjusted.Male.chrX.RData
transformed_methylation_adjusted.Male.chrY.RData
transformed_methylation_adjusted.RData
```
#### Script 3e
Similarly, for the `03e-methylation_adjustment2.sh` script you would create a submission script (e.g. `submit_03e.sh`):

```bash

#!/bin/bash

#SBATCH --job-name=meth_03e
#SBATCH --nodes=1
#SBATCH --mem=25G
#SBATCH --ntasks=1
#SBATCH --time=0-50:00:00
#SBATCH --array=1-100
#SBATCH --output=job_reports/slurm03e-%A_%a.out


set -e

echo "Running on ${HOSTNAME}"

cd /path/to/DEEP_mqtls/
./03e-methylation_adjustment2.sh ${SLURM_ARRAY_TASK_ID}
```

Then:
```
    sbatch submit_03e.sh
```

You can check if your jobs ran successfully by using this line in `results/03/logs_e`:
```
    cd results/03/logs_e
    grep "Successfully adjusting non-genetic PCs" log.txt[0-9]* |wc -l
```
100 should be returned if 100 batch jobs are successful.
and check the failed jobs by running
```
    find . -type f -exec grep -L "Successfully adjusting non-genetic PCs" {} +
```
and then resubmit the failed job by running 
```
source config
cd $home_directory
    ./03e-methylation_adjustment2.sh ${failed_task_id}
```
and once the jobs are completed, aggregating all subset RData:
```
    ./resources/methylation/aggregate_adjustment2.sh
```

Finally you need to check the log files if all probes are adjusted and "Successfully" should be printed three times in the logfile.

```
grep "The number of CpGs before and after adjustment are consistent" log.txt_aggregation
grep Successful ./results/03/logs_e/log.txt_aggregation | wc -l
```
