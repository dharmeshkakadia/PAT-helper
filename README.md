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

## Analyzing data
1. cd to the results dircetory for your run. For example, `cd PAT-master/PAT/results/tpch_query_1`.

2. Use the following commands to generate aggregates resource usage. The following command will generate aggregate for Network usage.
    ```shell
    find . -name netstat -type f -exec grep 'eth0' {} > temp  \; ; find . -name netstat -type f -exec head -1 {}  \; | uniq | sed 's/%//g' | sed 's:/::g' | {cat -; cat temp} > allnet.tsv 
    ```

3. The following command will generate aggregate for CPU usage.
    ```shell
    find . -name cpustat -type f -exec sed '1d' {} > temp \;  ; find . -name cpustat -type f -exec head -1 {}  \; | uniq | sed 's/%//g' | {cat -; cat temp} > allcpu.tsv 
    ```

4. You can further aggregate/join data using csvsql (from csvkit - install using `pip install csvkit`). Note it supports arbitrary SQL query against the csv/tsv files. Below is an example where we join the CPU and network aggregated data based on timestamps to give a single pane view of what was the status of the system over time.
    ```shell
    csvsql allnet.tsv allcpu.tsv --query "select a.timestamp,sum(txkbs)/1000000,sum(rxkbs)/1000000, avg(user), avg(system) from allnet a, allcpu b where a.timestamp=b.timestamp group by a.timestamp" > summary_stats.csv
    ```
