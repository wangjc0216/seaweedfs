# SeaweedFS


[![Build Status](https://travis-ci.org/chrislusf/seaweedfs.svg?branch=master)](https://travis-ci.org/chrislusf/seaweedfs)
[![GoDoc](https://godoc.org/github.com/chrislusf/seaweedfs/weed?status.svg)](https://godoc.org/github.com/chrislusf/seaweedfs/weed)
[![Wiki](https://img.shields.io/badge/docs-wiki-blue.svg)](https://github.com/chrislusf/seaweedfs/wiki)
[![Docker Pulls](https://img.shields.io/docker/pulls/chrislusf/seaweedfs.svg?maxAge=604800)](https://hub.docker.com/r/chrislusf/seaweedfs/)

![SeaweedFS Logo](https://raw.githubusercontent.com/chrislusf/seaweedfs/master/note/seaweedfs.png)

<h2 align="center">Supporting SeaweedFS</h2>

SeaweedFS is an independent Apache-licensed open source project with its ongoing development made 
possible entirely thanks to the support of these awesome [backers](https://github.com/chrislusf/seaweedfs/blob/master/backers.md). 
If you'd like to grow SeaweedFS even stronger, please consider joining our
<a href="https://www.patreon.com/seaweedfs">sponsors on Patreon</a>.

Platinum ($2500/month), Gold ($500/month): put your company logo on the SeaweedFS github page
Generous Backer($50/month), Backer($10/month): put your name on the SeaweedFS backer page.

Your support will be really appreciated by me and other supporters!

<h3 align="center"><a href="https://www.patreon.com/seaweedfs">Sponsor SeaweedFS via Patreon</a></h3>

<h4 align="center">Platinum</h4>

<p align="center">
  <a href="" target="_blank">
    Add your name or icon here
  </a>
</p>

<h4 align="center">Gold</h4>

<table>
  <tbody>
    <tr>
      <td align="center" valign="middle">
        <a href="" target="_blank">
          Add your name or icon here
        </a>
      </td>
    </tr>
    <tr></tr>
  </tbody>
</table>

---


- [Download Binaries for different platforms](https://github.com/chrislusf/seaweedfs/releases/latest)
- [SeaweedFS on Slack](https://join.slack.com/t/seaweedfs/shared_invite/enQtMzI4MTMwMjU2MzA3LTc4MmVlYmFlNjBmZTgzZmJlYmI1MDE1YzkyNWYyZjkwZDFiM2RlMDdjNjVlNjdjYzc4NGFhZGIyYzEyMzJkYTA)
- [SeaweedFS Mailing List](https://groups.google.com/d/forum/seaweedfs)
- [Wiki Documentation](https://github.com/chrislusf/seaweedfs/wiki)


## Introduction

SeaweedFS is a simple and highly scalable distributed file system. There are two objectives:

1. to store billions of files!
2. to serve the files fast!

SeaweedFS started as an Object Store to handle small files efficiently. Instead of managing all file metadata in a central master, the central master only manages file volumes, and it lets these volume servers manage files and their metadata. This relieves concurrency pressure from the central master and spreads file metadata into volume servers, allowing faster file access (just one disk read operation).

There is only 40 bytes of disk storage overhead for each file's metadata. It is so simple with O(1) disk reads that you are welcome to challenge the performance with your actual use cases.

SeaweedFS started by implementing [Facebook's Haystack design paper](http://www.usenix.org/event/osdi10/tech/full_papers/Beaver.pdf).

SeaweedFS can work very well with just the object store. [[Filer]] can then be added later to support directories and POSIX attributes. Filer is a separate linearly-scalable stateless server with customizable metadata stores, e.g., MySql/Postgres/Redis/Cassandra/LevelDB.
# 下载路径
**(下载路径)https://github.com/chrislusf/seaweedfs/releases**

## Additional Features
* Can choose no replication or different replication levels, rack and data center aware
* Automatic master servers failover - no single point of failure (SPOF)
* 
* Automatic Gzip compression depending on file mime type
* 根据文件的mime类型自动Gzip压缩   mime类型参考：https://www.cnblogs.com/jsean/articles/1610265.html
* Automatic compaction to reclaim disk space after deletion or update
* 自动压实恢复磁盘空间在删除和更新之后
* Servers in the same cluster can have different disk spaces, file systems, OS etc.
* 同一个集群中的服务器可以有不同的磁盘空间，文件系统，操作系统等等。
* Adding/Removing servers does **not** cause any data re-balancing
* 添加/删除 服务器不会引起任何数据调整
* Optionally fix the orientation for jpeg pictures 
* 随意的修改图片方向
* Support Etag, Accept-Range, Last-Modified, etc.
* 支持 etag， Accept-Range, Last-Modified 相关etag的信息：https://blog.csdn.net/t12x3456/article/details/17301897
* Support in-memory/leveldb/boltdb/btree mode tuning for memory/performance balance.
* 支持 内存/leveldb/boltdb/btreee 模式 调节内存性能平衡  性能平衡的文章：https://www.jianshu.com/p/0f330628fd41 
* 关于leveldb/boltdb 的资料 可以查看 https://juejin.im/entry/5a93a776f265da4e9c634b87

## Filer Features
* [filer server][Filer] provide "normal" directories and files via http.
* [mount filer][Mount] to read and write files directly as a local directory via FUSE.
* [Amazon S3 compatible API][AmazonS3API] to access files with S3 tooling.
* [Hadoop Compatible File System][Hadoop] to access files from Hadoop/Spark/Flink/etc jobs.
* [Async Backup To Cloud][BackupToCloud] has extremely fast local access and backups to Amazon S3, Google Cloud Storage, Azure, BackBlaze.

[Filer]: https://github.com/chrislusf/seaweedfs/wiki/Directories-and-Files
[Mount]: https://github.com/chrislusf/seaweedfs/wiki/Mount
[AmazonS3API]: https://github.com/chrislusf/seaweedfs/wiki/Amazon-S3-API
[BackupToCloud]: https://github.com/chrislusf/seaweedfs/wiki/Backup-to-Cloud
[Hadoop]: https://github.com/chrislusf/seaweedfs/wiki/Hadoop-Compatible-File-System

## Example Usage
By default, the master node runs on port 9333, and the volume nodes run on port 8080.
Let's start one master node, and two volume nodes on port 8080 and 8081. Ideally, they should be started from different machines. We'll use localhost as an example.

SeaweedFS uses HTTP REST operations to read, write, and delete. The responses are in JSON or JSONP format.

### Start Master Server

```
> ./weed master
```

### Start Volume Servers ###

```
> weed volume -dir="/tmp/data1" -max=5  -mserver="localhost:9333" -port=8080 &
> weed volume -dir="/tmp/data2" -max=10 -mserver="localhost:9333" -port=8081 &
```

### Write File ###

To upload a file: first, send a HTTP POST, PUT, or GET request to `/dir/assign` to get an `fid` and a volume server url:

```
> curl http://localhost:9333/dir/assign
{"count":1,"fid":"3,01637037d6","url":"127.0.0.1:8080","publicUrl":"localhost:8080"}
```

Second, to store the file content, send a HTTP multi-part POST request to `url + '/' + fid` from the response:

```
> curl -F file=@/home/chris/myphoto.jpg http://127.0.0.1:8080/3,01637037d6
{"size": 43234}
```

To update, send another POST request with updated file content.

For deletion, send an HTTP DELETE request to the same `url + '/' + fid` URL:

```
> curl -X DELETE http://127.0.0.1:8080/3,01637037d6
```
### Save File Id ###

Now, you can save the `fid`, 3,01637037d6 in this case, to a database field.

The number 3 at the start represents a volume id. After the comma, it's one file key, 01, and a file cookie, 637037d6.

The volume id is an unsigned 32-bit integer. The file key is an unsigned 64-bit integer. The file cookie is an unsigned 32-bit integer, used to prevent URL guessing.

The file key and file cookie are both coded in hex. You can store the <volume id, file key, file cookie> tuple in your own format, or simply store the `fid` as a string.

If stored as a string, in theory, you would need 8+1+16+8=33 bytes. A char(33) would be enough, if not more than enough, since most uses will not need 2^32 volumes.

If space is really a concern, you can store the file id in your own format. You would need one 4-byte integer for volume id, 8-byte long number for file key, and a 4-byte integer for the file cookie. So 16 bytes are more than enough.

### Read File ###

Here is an example of how to render the URL.

First look up the volume server's URLs by the file's volumeId:

```
> curl http://localhost:9333/dir/lookup?volumeId=3
{"locations":[{"publicUrl":"localhost:8080","url":"localhost:8080"}]}
```

Since (usually) there are not too many volume servers, and volumes don't move often, you can cache the results most of the time. Depending on the replication type, one volume can have multiple replica locations. Just randomly pick one location to read.

Now you can take the public url, render the url or directly read from the volume server via url:

```
 http://localhost:8080/3,01637037d6.jpg
```

Notice we add a file extension ".jpg" here. It's optional and just one way for the client to specify the file content type.

If you want a nicer URL, you can use one of these alternative URL formats:

```
 http://localhost:8080/3/01637037d6/my_preferred_name.jpg
 http://localhost:8080/3/01637037d6.jpg
 http://localhost:8080/3,01637037d6.jpg
 http://localhost:8080/3/01637037d6
 http://localhost:8080/3,01637037d6
```

If you want to get a scaled version of an image, you can add some params:

```
http://localhost:8080/3/01637037d6.jpg?height=200&width=200
http://localhost:8080/3/01637037d6.jpg?height=200&width=200&mode=fit
http://localhost:8080/3/01637037d6.jpg?height=200&width=200&mode=fill
```

### Rack-Aware and Data Center-Aware Replication ###

SeaweedFS applies the replication strategy at a volume level. So, when you are getting a file id, you can specify the replication strategy. For example:

```
curl http://localhost:9333/dir/assign?replication=001
```

The replication parameter options are:

```
000: no replication
001: replicate once on the same rack
010: replicate once on a different rack, but same data center
100: replicate once on a different data center
200: replicate twice on two different data center
110: replicate once on a different rack, and once on a different data center
```

More details about replication can be found [on the wiki][Replication].

[Replication]: https://github.com/chrislusf/seaweedfs/wiki/Replication

You can also set the default replication strategy when starting the master server.

### Allocate File Key on specific data center ###

Volume servers can be started with a specific data center name:

```
 weed volume -dir=/tmp/1 -port=8080 -dataCenter=dc1
 weed volume -dir=/tmp/2 -port=8081 -dataCenter=dc2
```

When requesting a file key, an optional "dataCenter" parameter can limit the assigned volume to the specific data center. For example, this specifies that the assigned volume should be limited to 'dc1':

```
 http://localhost:9333/dir/assign?dataCenter=dc1
```

### Other Features ###
  * [No Single Point of Failure][feat-1]
  * [Insert with your own keys][feat-2]
  * [Chunking large files][feat-3]
  * [Collection as a Simple Name Space][feat-4]

[feat-1]: https://github.com/chrislusf/seaweedfs/wiki/Failover-Master-Server
[feat-2]: https://github.com/chrislusf/seaweedfs/wiki/Optimization#insert-with-your-own-keys
[feat-3]: https://github.com/chrislusf/seaweedfs/wiki/Optimization#upload-large-files
[feat-4]: https://github.com/chrislusf/seaweedfs/wiki/Optimization#collection-as-a-simple-name-space

## Architecture ##

Usually distributed file systems split each file into chunks, a central master keeps a mapping of filenames, chunk indices to chunk handles, and also which chunks each chunk server has.

The main drawback is that the central master can't handle many small files efficiently, and since all read requests need to go through the chunk master, so it might not scale well for many concurrent users.

Instead of managing chunks, SeaweedFS manages data volumes in the master server. Each data volume is 32GB in size, and can hold a lot of files. And each storage node can have many data volumes. So the master node only needs to store the metadata about the volumes, which is a fairly small amount of data and is generally stable.

The actual file metadata is stored in each volume on volume servers. Since each volume server only manages metadata of files on its own disk, with only 16 bytes for each file, all file access can read file metadata just from memory and only needs one disk operation to actually read file data.

For comparison, consider that an xfs inode structure in Linux is 536 bytes.

### Master Server and Volume Server ###

The architecture is fairly simple. The actual data is stored in volumes on storage nodes. One volume server can have multiple volumes, and can both support read and write access with basic authentication.

All volumes are managed by a master server. The master server contains the volume id to volume server mapping. This is fairly static information, and can be easily cached.

On each write request, the master server also generates a file key, which is a growing 64-bit unsigned integer. Since write requests are not generally as frequent as read requests, one master server should be able to handle the concurrency well.

### Write and Read files ###

When a client sends a write request, the master server returns (volume id, file key, file cookie, volume node url) for the file. The client then contacts the volume node and POSTs the file content.

When a client needs to read a file based on (volume id, file key, file cookie), it asks the master server by the volume id for the (volume node url, volume node public url), or retrieves this from a cache. Then the client can GET the content, or just render the URL on web pages and let browsers fetch the content.

Please see the example for details on the write-read process.

### Storage Size ###

In the current implementation, each volume can hold 32 gibibytes (32GiB or 8x2^32 bytes). This is because we align content to 8 bytes. We can easily increase this to 64GiB, or 128GiB, or more, by changing 2 lines of code, at the cost of some wasted padding space due to alignment.

There can be 4 gibibytes (4GiB or 2^32 bytes) of volumes. So the total system size is 8 x 4GiB x 4GiB which is 128 exbibytes (128EiB or 2^67 bytes).

Each individual file size is limited to the volume size.

### Saving memory ###

All file meta information stored on an volume server is readable from memory without disk access. Each file takes just a 16-byte map entry of <64bit key, 32bit offset, 32bit size>. Of course, each map entry has its own space cost for the map. But usually the disk space runs out before the memory does.

## Compared to Other File Systems ##

Most other distributed file systems seem more complicated than necessary.

SeaweedFS is meant to be fast and simple, in both setup and operation. If you do not understand how it works when you reach here, we've failed! Please raise an issue with any questions or update this file with clarifications.

### Compared to HDFS ###

HDFS uses the chunk approach for each file, and is ideal for storing large files.

SeaweedFS is ideal for serving relatively smaller files quickly and concurrently.

SeaweedFS can also store extra large files by splitting them into manageable data chunks, and store the file ids of the data chunks into a meta chunk. This is managed by "weed upload/download" tool, and the weed master or volume servers are agnostic about it.


### Compared to GlusterFS, Ceph ###

The architectures are mostly the same. SeaweedFS aims to store and read files fast, with a simple and flat architecture. The main differences are

- [SeaweedFS](#seaweedfs)
  - [Introduction](#introduction)
- [下载路径](#%E4%B8%8B%E8%BD%BD%E8%B7%AF%E5%BE%84)
  - [Additional Features](#additional-features)
  - [Filer Features](#filer-features)
  - [Example Usage](#example-usage)
    - [Start Master Server](#start-master-server)
    - [Start Volume Servers](#start-volume-servers)
    - [Write File](#write-file)
    - [Save File Id](#save-file-id)
    - [Read File](#read-file)
    - [Rack-Aware and Data Center-Aware Replication](#rack-aware-and-data-center-aware-replication)
    - [Allocate File Key on specific data center](#allocate-file-key-on-specific-data-center)
    - [Other Features](#other-features)
  - [Architecture](#architecture)
    - [Master Server and Volume Server](#master-server-and-volume-server)
    - [Write and Read files](#write-and-read-files)
    - [Storage Size](#storage-size)
    - [Saving memory](#saving-memory)
  - [Compared to Other File Systems](#compared-to-other-file-systems)
    - [Compared to HDFS](#compared-to-hdfs)
    - [Compared to GlusterFS, Ceph](#compared-to-glusterfs-ceph)
    - [Compared to GlusterFS](#compared-to-glusterfs)
    - [Compared to Ceph](#compared-to-ceph)
  - [Dev plan](#dev-plan)
  - [Installation guide for users who are not familiar with golang](#installation-guide-for-users-who-are-not-familiar-with-golang)
  - [Disk Related topics](#disk-related-topics)
    - [Hard Drive Performance](#hard-drive-performance)
    - [Solid State Disk](#solid-state-disk)
  - [Benchmark](#benchmark)
  - [License](#license)
  - [Stargazers over time](#stargazers-over-time)

| System         | File Meta                       | File Content Read| POSIX  | REST API | Optimized for small files |
| -------------  | ------------------------------- | ---------------- | ------ | -------- | ------------------------- |
| SeaweedFS      | lookup volume id, cacheable     | O(1) disk seek   |        | Yes      | Yes                       |
| SeaweedFS Filer| Linearly Scalable, Customizable | O(1) disk seek   | FUSE   | Yes      | Yes                       |
| GlusterFS      | hashing          |                  | FUSE, NFS          |          |                           |
| Ceph           | hashing + rules  |                  | FUSE               | Yes      |                           |

### Compared to GlusterFS ###

GlusterFS stores files, both directories and content, in configurable volumes called "bricks".

GlusterFS hashes the path and filename into ids, and assigned to virtual volumes, and then mapped to "bricks".

### Compared to Ceph ###

Ceph can be setup similar to SeaweedFS as a key->blob store. It is much more complicated, with the need to support layers on top of it. [Here is a more detailed comparison](https://github.com/chrislusf/seaweedfs/issues/120)

SeaweedFS has a centralized master group to look up free volumes, while Ceph uses hashing and metadata servers to locate its objects. Having a centralized master makes it easy to code and manage. 

Same as SeaweedFS, Ceph is also based on the object store RADOS. Ceph is rather complicated with mixed reviews.

Ceph uses CRUSH hashing to automatically manage the data placement. SeaweedFS places data by assigned volumes.

SeaweedFS is optimized for small files. Small files are stored as one continuous block of content, with at most 8 unused bytes between files. Small file access is O(1) disk read.

SeaweedFS Filer uses off-the-shelf stores, such as MySql, Postgres, Redis, Cassandra, to manage file directories. There are proven, scalable, and easier to manage.

| SeaweedFS         | comparable to Ceph | advantage |
| -------------  | ------------- | ---------------- |
| Master  | MDS | simpler |
| Volume  | OSD | optimized for small files |
| Filer  | Ceph FS | linearly scalable, Customizable, O(1) or O(logN) |


## Dev plan ##

More tools and documentation, on how to maintain and scale the system. For example, how to move volumes, automatically balancing data, how to grow volumes, how to check system status, etc.
Other key features include: Erasure Encoding, JWT security.

This is a super exciting project! And we need helpers and [support](https://www.patreon.com/seaweedfs)!


## Installation guide for users who are not familiar with golang

Step 1: install go on your machine and setup the environment by following the instructions at:

https://golang.org/doc/install

make sure you set up your $GOPATH


Step 2: also you may need to install Mercurial by following the instructions at:

http://mercurial.selenic.com/downloads

Step 3: download, compile, and install the project by executing the following command

```bash
go get github.com/chrislusf/seaweedfs/weed
```

Once this is done, you will find the executable "weed" in your `$GOPATH/bin` directory

Step 4: after you modify your code locally, you could start a local build by calling `go install` under 

```
$GOPATH/src/github.com/chrislusf/seaweedfs/weed
```

## Disk Related topics ##

### Hard Drive Performance ###

When testing read performance on SeaweedFS, it basically becomes a performance test of your hard drive's random read speed. Hard drives usually get 100MB/s~200MB/s.

### Solid State Disk

To modify or delete small files, SSD must delete a whole block at a time, and move content in existing blocks to a new block. SSD is fast when brand new, but will get fragmented over time and you have to garbage collect, compacting blocks. SeaweedFS is friendly to SSD since it is append-only. Deletion and compaction are done on volume level in the background, not slowing reading and not causing fragmentation.

## Benchmark

My Own Unscientific Single Machine Results on Mac Book with Solid State Disk, CPU: 1 Intel Core i7 2.6GHz.

Write 1 million 1KB file:
```
Concurrency Level:      16
Time taken for tests:   88.796 seconds
Complete requests:      1048576
Failed requests:        0
Total transferred:      1106764659 bytes
Requests per second:    11808.87 [#/sec]
Transfer rate:          12172.05 [Kbytes/sec]

Connection Times (ms)
              min      avg        max      std
Total:        0.2      1.3       44.8      0.9

Percentage of the requests served within a certain time (ms)
   50%      1.1 ms
   66%      1.3 ms
   75%      1.5 ms
   80%      1.7 ms
   90%      2.1 ms
   95%      2.6 ms
   98%      3.7 ms
   99%      4.6 ms
  100%     44.8 ms
```

Randomly read 1 million files:
```
Concurrency Level:      16
Time taken for tests:   34.263 seconds
Complete requests:      1048576
Failed requests:        0
Total transferred:      1106762945 bytes
Requests per second:    30603.34 [#/sec]
Transfer rate:          31544.49 [Kbytes/sec]

Connection Times (ms)
              min      avg        max      std
Total:        0.0      0.5       20.7      0.7

Percentage of the requests served within a certain time (ms)
   50%      0.4 ms
   75%      0.5 ms
   95%      0.6 ms
   98%      0.8 ms
   99%      1.2 ms
  100%     20.7 ms
```


## License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.


## Stargazers over time

[![Stargazers over time](https://starcharts.herokuapp.com/chrislusf/seaweedfs.svg)](https://starcharts.herokuapp.com/chrislusf/seaweedfs)
