# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** _Script 0_ and _01a_

**Data upload method:** automated upload to sftp bristol



### Now upload the results

To check that everything ran successfully, please run:

```
./check_upload.sh 01 check
```

This should tell you that `Section 01 has been successfully completed!`. Now please go to the Results directory and check your results.
```
cd results/01
```


Now please go back to the DEEP_mqtls directory and upload the results like this:

```
./check_upload.sh 01 upload
```

It will make sure everything looks correct and connect to the sftp server. It will request your password (this should have been provided to you along with your username). Once you have entered your password it will upload the results files from `section 01`.
