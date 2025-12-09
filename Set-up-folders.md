# MODULE STATUS

**Developer:** Developer's team

**Scripts status:** Ready

**Prerequisite scripts:** None

**Data upload method:** None

**Issue report to:** Haotian Tang <haotian.tang@bristol.ac.uk>

# Set up folders

We have created a script that will generate folders for each module. It also makes the bash scripts executable.

It will generate folders for:

- processed_data
- results
- logfiles
- job_reports
- hase data

To run:
```
    ./00-setup_folders.sh -c /path/to/config/file
```
This will be very quick to run.