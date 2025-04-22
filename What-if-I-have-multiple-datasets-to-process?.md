The pipeline has been designed such that if you have multiple datasets, you only need to maintain one copy of the repository. This is preferable and it means it is easily to keep up to date and saves on space. 

The pipeline assumes by default that your config file in located in your scripts directory so the pipeline can find it easily. However, if you have multiple datasets you will need multiple config files, one per each dataset as well as multiple `home_directory`. There is the option to run each script in the pipeline with a custom path to the config file. This means you can maintain multiple config files, one for each dataset, on your system at different file paths (for example within the `home_directory`).

If you want to run a config file that is not located in the `scripts_directory` you add the flag `-c` followed by the path to the config file you want to include on that execution. You need to do this for every script in the pipeline. For example in the setup script described below you can run: 

```
./00-setup_folders.sh -c /path/to/config/file
```

By directly specifying the config file you want to use, you can easily run the same pipeline on different sets of data. 

