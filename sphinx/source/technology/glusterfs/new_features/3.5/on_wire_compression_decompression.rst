-----------------------------------
On-Wire Compression + Decompression
-----------------------------------

Use of this feature reduces the overall network overhead for Gluster operations from a client. Depending on workload, this could show dramatic increases in the performance of Gluster volumes. This feature also allows a good trade-off of CPU to network resources, which will be a boon to most users as CPU is not generally being consumed to anywhere near its full potential, whereas network has traditionally been the bottleneck in high performance workloads.


Setup
=====

::

  # brick="/mnt/lv3/vol-comp"; gluster volume create vol-comp eins:$brick
  volume create: vol-comp: success: please start the volume to access data
  # gluster volume start vol-comp
  volume start: vol-comp: success
  # gluster volume set vol-comp network.compression on
  volume set: success
  
  [2014-05-18 23:51:50]# gluster volume set vol-comp network.compression.compression-level 8
  volume set: success
  [2014-05-18 23:56:49]# gluster volume set vol-comp network.compression.min-size 50
  volume set: success
  [2014-05-18 23:57:02]# gluster volume info vol-comp
  
  Volume Name: vol-comp
  Type: Distribute
  Volume ID: 92b47734-2552-4168-b3c3-151093562e4f
  Status: Started
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv3/vol-comp
  Options Reconfigured:
  network.compression.min-size: 50
  network.compression.compression-level: 8
  network.compression.mode: server
  network.compression: on
  
  # mount -t glusterfs localhost:/vol-comp /mnt/glusterfs/vol-comp

Do
==

::

  # dd if=/dev/zero of=/mnt/glusterfs/vol-comp/1gb.dat bs=1M count=1024
  dd: writing `/mnt/glusterfs/vol-comp/1gb.dat': Input/output error
  dd: closing output file `/mnt/glusterfs/vol-comp/1gb.dat': Input/output error
  
  
  [2014-05-18 14:59:24.034499] W [fuse-bridge.c:2201:fuse_writev_cbk] 0-glusterfs-fuse: 12: WRITE => -1 (Input/output error)
  [2014-05-18 14:59:24.034917] W [fuse-bridge.c:2201:fuse_writev_cbk] 0-glusterfs-fuse: 14: WRITE => -1 (Input/output error)
  [2014-05-18 14:59:24.035439] W [fuse-bridge.c:1206:fuse_err_cbk] 0-glusterfs-fuse: 15: FLUSH() ERR => -1 (Input/output error)
  [2014-05-18 14:59:59.799649] W [fuse-bridge.c:2201:fuse_writev_cbk] 0-glusterfs-fuse: 46: WRITE => -1 (Input/output error)
  [2014-05-18 14:59:59.800123] W [fuse-bridge.c:2201:fuse_writev_cbk] 0-glusterfs-fuse: 48: WRITE => -1 (Input/output error)
  [2014-05-18 14:59:59.804226] W [fuse-bridge.c:1206:fuse_err_cbk] 0-glusterfs-fuse: 49: FLUSH() ERR => -1 (Input/output error)
  [2014-05-18 15:00:36.742227] W [fuse-bridge.c:2201:fuse_writev_cbk] 0-glusterfs-fuse: 61: WRITE => -1 (Input/output error)
  [2014-05-18 15:00:36.742738] W [fuse-bridge.c:2201:fuse_writev_cbk] 0-glusterfs-fuse: 63: WRITE => -1 (Input/output error)
  [2014-05-18 15:00:36.745857] W [fuse-bridge.c:1206:fuse_err_cbk] 0-glusterfs-fuse: 64: FLUSH() ERR => -1 (Input/output error)
  
  # gluster volume reset vol-comp
  volume reset: success: reset volume successful
  
  # dd if=/dev/zero of=/mnt/glusterfs/vol-comp/1gb.dat bs=1M count=1024
  1024+0 records in
  1024+0 records out
  1073741824 bytes (1.1 GB) copied, 9.14845 s, 117 MB/s
  
  # gluster volume set vol-comp network.compression on
  volume set: success
  
  # dd if=/dev/zero of=/mnt/glusterfs/vol-comp/1gb.dat bs=1M count=1024
  dd: writing `/mnt/glusterfs/vol-comp/1gb.dat': Input/output error
  dd: closing output file `/mnt/glusterfs/vol-comp/1gb.dat': Input/output error
  
  
  
  # gluster volume set vol-comp performance.strict-write-ordering on
  volume set: success
  
  # gluster volume info vol-comp
  
  Volume Name: vol-comp
  Type: Distribute
  Volume ID: 92b47734-2552-4168-b3c3-151093562e4f
  Status: Started
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv3/vol-comp
  Options Reconfigured:
  performance.strict-write-ordering: on
  network.compression.mode: server
  network.compression: on
  
  
  # dd if=/dev/zero of=/mnt/glusterfs/vol-comp/1gb.dat bs=1M count=1024
  dd: writing `/mnt/glusterfs/vol-comp/1gb.dat': Input/output error
  dd: closing output file `/mnt/glusterfs/vol-comp/1gb.dat': Input/output error
  
  
  # gluster volume set vol-comp performance.write-behind off
  volume set: success
  
  
  # dd if=/dev/zero of=/mnt/glusterfs/vol-comp/1gb.dat bs=1M count=1024
  dd: writing `/mnt/glusterfs/vol-comp/1gb.dat'
  1+0 records in
  0+0 records out
  1048577 bytes (1.0 MB) copied, 21.8345 s, 48.0 kB/s
  
  # ls -lh /mnt/glusterfs/vol-comp
  total 1.1M
  -rw-r--r-- 1 root root 1.1M May 19 00:10 1gb.dat
  
  # echo "test" > /mnt/glusterfs/vol-comp/test.txt
  echo: write error: success
  
  # cp /tmp/test.txt /mnt/glusterfs/vol-comp/
  cp: writing `/mnt/glusterfs/vol-comp/test.txt': Bad address
  
  
  
  # gluster volume set vol-comp network.compression.compression-level 0
  volume set: success
  
  # cp /tmp/test.txt /mnt/glusterfs/vol-comp/
  cp: writing `/mnt/glusterfs/vol-comp/test.txt': Bad address
  
  # file /mnt/glusterfs/vol-comp/test.txt
  /mnt/glusterfs/vol-comp/test.txt: data
  
  # file /tmp/test.txt
  /tmp/test.txt: ASCII text
  
  # diff /mnt/glusterfs/vol-comp/test.txt /tmp/test.txt
  Binary files /mnt/glusterfs/vol-comp/test.txt and /tmp/test.txt differ
  
  [eins]# file /mnt/lv3/vol-comp/test.txt
  /mnt/lv3/vol-comp/test.txt: data


Success
=======

::

  # gluster volume info vol-comp
  
  Volume Name: vol-comp
  Type: Distribute
  Volume ID: 92b47734-2552-4168-b3c3-151093562e4f
  Status: Started
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv3/vol-comp
  Options Reconfigured:
  network.compression.compression-level: 0
  performance.open-behind: off
  performance.write-behind: off
  performance.strict-write-ordering: on
  network.compression.mode: server
  network.compression: on
  
  # gluster volume set vol-comp network.compression.compression-level 9
  volume set: success
  
  # cp /tmp/test.txt /mnt/glusterfs/vol-comp/
  
  
  # dd if=/dev/zero of=/mnt/glusterfs/vol-comp/1gb.dat bs=1M count=1024
  1024+0 records in
  1024+0 records out
  1073741824 bytes (1.1 GB) copied, 33.8606 s, 31.7 MB/s
  [2014-05-19 0:25:40]# diff /mnt/glusterfs/vol-comp/1gb.dat /tmp/1gb.dat


