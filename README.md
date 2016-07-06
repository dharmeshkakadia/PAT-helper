# PAT-helper
Helper tool for https://github.com/intel-hadoop/PAT

## What does the helper script do?
1. Setup passwordless ssh required by PAT.
2. Download, configure and run PAT tool.

## How to use
  1.	Download the helper [pat-helper.sh](https://github.com/dharmeshkakadia/PAT-helper/blob/master/pat-helper.sh) script.
  2.	Create an *executable* script that starts your workload. It can be any set of commands. For example simple workload.sh script can  contain,
  
    ```shell
    #!/bin/bash
    hive -S -e "use tpch_tezt_2; select count(*) from customer ;" > test.out 2>&1
    ```
  3.	Run the script : ./pat-helper.sh YourPassword workload.sh

The above step will generate a results folder with a timestamp under PAT-master/PAT/results/. You can download it to your machine. Open instruments/result_templatev1.xlsm in excel and press CTRL+Q. This will aggregate the collected stats and plot them.
