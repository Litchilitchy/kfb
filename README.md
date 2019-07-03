# KFBio project

## Prerequisites
* Analytics zoo
* cpp (kfbReader, etc) provided, add the directory of `.so` to `ld.so.conf.d/kfb.conf` (name of `kfb.conf` does not matter)
* Spark 2.4.3 +
* Redis

## Steps to run
First get the machine ready

`systemctl start docker.service`

start redis `docker run --name test-redis -p 6379:6379 -d redis`, if it is in history, then use `docker history ls -a` get corresponding container id and `docker start ${id}`

`python py/monitor.py` to prepare for output

`spark-submit --class com.intel... --modelPath ...` to start streaming

`python py/cut_kfb_image.py` to cut KFB image into pieces

## Issue notes
* Thread limit: for 16 threads cutting image error, reduce thread number to 8, this may due to `ReadRoiData` limitation of `.so` code

* use `python3` to run cut-image and `python` to run monitor, may due to python 2 & 3 incompatibility of unicode

* Do not do things to `batchDF`, according to discovered issues, use `batchDF.isEmpty` will lead to missing of some data, use `batchDF.collect()` or `batchDF.count()` will lead to disappear of this DataFrame
