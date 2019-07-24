# KFBio project

## Prerequisites
* Analytics zoo
* cpp (kfbReader, etc) provided, add the directory of `.so` to `ld.so.conf.d/kfb.conf` (name of `kfb.conf` does not matter)
* Spark 2.4.3 +
* Redis

## Steps to run
First get the machine ready

`systemctl start docker.service`

start redis `docker run --name test-redis -p 6379:6379 -d redis`, if it is in history, then use `docker container ls -a` get corresponding container id and `docker start ${id}`

* monitor: `python py/monitor.py --file_path $ResultPath` to prepare for output

* zoo-streaming: `spark-submit --class com.intel... --modelPath ... -o $ResultPath` to start streaming (note this $ResultPath must match the one of monitor above)

* cut image: `python py/cut_kfb_image.py` to cut KFB image into pieces

## Issue notes
* Thread limit: for 16 threads cutting image error, reduce thread number to 8, this may due to `ReadRoiData` limitation of `.so` code

* (maybe issue) use `python3` to run cut-image and `python` to run monitor, may due to python 2 & 3 incompatibility of unicode

* Do not do things to `batchDF`, according to discovered issues, use `batchDF.isEmpty` will lead to missing of some data, use `batchDF.collect()` or `batchDF.count()` will lead to disappear of this DataFrame

* Redis issue: the records in Redis may be limited, so if the consumer has small bandwidth, then the producer should control the average bandwidth. e.g. limit the kfb images to 3 in once cut. (If the cut process generate images of 10 kfb and the consumer could not consume as soon as possible then Redis might crashs). For more details of this issue, see https://stackoverflow.com/questions/19581059/misconf-redis-is-configured-to-save-rdb-snapshots
