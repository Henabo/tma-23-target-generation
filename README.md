# TMA'23 Paper Data

This repository provides instructions and data which can be used to reproduce the results presented in our paper `Target Acquired? Evaluating Target Generation Algorithms for IPv6`.
该存储库提供的指令和数据可用于重现我们的论文“Target Acquired?”评估IPv6目标生成算法。

## Requirements

In order to run the included Python scripts, please install the requirements with `pip3 install -r requirements.txt`
为了运行包含的Python脚本，请使用' pip3 install -r requirements.txt '安装需求

## Target Generation

### Running the Algorithms

In order to run the algorithms, we provide a runner script, which you can use in the following way:

为了运行算法，我们提供了一个运行器脚本，您可以通过以下方式使用:

- Prerequisites are a debian-based system with CUDA drivers installed

安装了CUDA驱动的debian系统

- Run `./run.sh SETUP` to install all necessary packages. A new directory called `generation-$datetime` will have been created

—执行“。/ Run .sh SETUP”，安装所有需要的软件包。将创建一个名为“generation-$datetime”的新目录

- Run `./run.sh DOWNLOAD $newdir` to download the current hitlist as seed data.

—执行“。/ Run .sh DOWNLOAD $newdir”，下载当前热门列表作为种子数据。

- Run `./run.sh CATEGORIZE $newdir` to create one new directory per category with holds categorized seeds.

运行' ./ Run .sh CATEGORIZE $newdir '为每个类别创建一个新目录，其中包含已分类的种子。

- Run `./run.sh ALL` and specify the directory which you want to use as seeds (`$newdir` for full hitlist input for example). You can also switch out `ALL` to whichever algorith you want to run specifically.

运行' ./ Run .sh ALL '并指定你想要用作种子的目录(例如，' $newdir '用于完整的hitlist输入)。您还可以将' ALL '切换为您想要具体运行的任何算法。

### Results

In order to reproduce our results regarding the Target Generation Algorithms (TGAs) analyzed in our paper, we provide the following data:
为了重现我们在论文中分析的目标生成算法(TGAs)的结果，我们提供了以下数据:

- `generation_*`: results of running the TGAs with categorized input:
    - `results`: resulting candidate sets per TGA
    - `seeds`: seed dataset used for the generation run
- `scan*`: scan results collected when scanning the combined candidate sets of the algorithms. 扫描算法组合候选集时收集的扫描结果
    - `*.iponly`: filtered input files for the scan, 过滤输入文件进行扫描
    - `*.csv.*`: results specific to a protocol (or combined, in the case of `*.csv.total`)特定于协议的结果(或组合，在' *.csv.total '的情况下)

First steps to analyze the results:
分析结果的第一步:

- combine the data of both scans by running `./combine.sh` in the scan directory.
- run the scan analysis script by running

—在扫描目录下执行。/combine.sh命令，合并两次扫描的数据。
—执行命令运行扫描分析脚本

```
python3 analyze_scan.py
    --scanresults scan_2023-03-23/2023-03-23-combined.csv.*
    --gendirs .
    --num-workers 6
    --scanfile scan_2023-03-23/2023-03-23-combined.txt.expl.sortu.shuf.wl.bl.dpd.nondense.iponly
    --tmpdir tmp
```

The number of used worker threads as well as the tmp directory can be adapated.
After this, the jupyter notebook `visualizations.ipynb` can be executed according to the instructions in the notebook.
使用的工作线程的数量以及tmp目录都可以调整。
在这之后，木星笔记本的可视化。Ipynb '可以按照笔记本上的说明执行。


## Historic Hitlist statistics

In order to reproduce our historic results regarding the IPv6 Hitlist service, the following steps have to be taken:

- apply for access to the registered-only data of the Histlist service at the [website](https://ipv6hitlist.github.io/)
- download all historic data (takes a lot of space, which is why we don't provide it in this dataset)
- download all historic pyasn data with `./download_pyasn.sh` (this will take quite some time)
- download the latest peeringdb data set with `curl -L -o peeringdb.json "https://publicdata.caida.org/datasets/peeringdb/$(date -d yesterday +%Y)/$(date -d yesterday +%m)/peeringdb_2_dump_$(date -d yesterday +%Y_%m_%d).json"`
- decompress and append all downloaded data with asn info, e.g. with `for f in $DOWNLOAD_DIR/*/*.csv.xz; do python3 append_as_to_csv.py --asndb-directory $PYASN_DIR --input $f --output $OUTPUT; done`
- make all entries unique by running `mkdir $OUTPUT_SORTED; for f in $OUTPUT/*; do sort -u $f > $OUTPUT_SORTED/$(basename $f); done`
- generate the list of IPs per datapoint which respond to at least one protocol (all protocols combined, extension "total") by running `./combine_all.sh` in the `$OUTPUT` directory
- generate IP stability data by running `python3 analyze_ip_stability.py 2018-07-01 --extension total --base-dir $OUTPUT`, followed by `python3 analyze_ip_stability.py 2018-07-01.total.ipstability`
- lastly, run `python3 generate_stability_plot.py 2018-07-01.total.ipstability.ipdata $PEERINGDB` to reproduce the boxplots (Figure 4) from the paper

## Current Hitlist statistics

In order to reproduce the results about the current state of the IPv6 Hitlist service, the following steps have to be taken:

- run steps for historic results
- run the steps from `Target Generation -> Results`
- identify the last available input scan file (`input` directory of the registered-only data section of the hitlist)
- execute the cells in the last part of `visualizations.ipynb` according to the instructions
