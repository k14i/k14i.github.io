---------------
Disk encryption
---------------

Implement the previous work done in HekaFS into Gluster. This allows a volume (or per-tenant part of a volume) to be encrypted “at rest” on the server using keys only available on the client. [Note: We encrypt only content of regular files. File names are not encrypted! Also, encryption does not work in NFS mounts.]

Setting
=======

::

  # gluster volume info
  Volume Name: vol2
  Type: Replicate
  Volume ID: e0332771-a3c2-4fe5-980c-b3860cfe3baf
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  
  # gluster volume set vol2 encryption on
  volume set: success
  
  # gluster volume info
  Volume Name: vol2
  Type: Replicate
  Volume ID: e0332771-a3c2-4fe5-980c-b3860cfe3baf
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  Options Reconfigured:
  features.encryption: on
  
  # for x in quick-read write-behind open-behind; do gluster volume set vol2 performance.$x off; done
  volume set: success
  volume set: success
  volume set: success
  
  # gluster volume set vol2 encryption.master-key /var/lib/glusterd/vols/vol2/encryption.master-key
  volume set: success
  
  # openssl rand -hex 32 > /var/lib/glusterd/vols/vol2/encryption.master-key
  
  # gluster volume set vol2 encryption.data-key-size 512
  volume set: success
  
  # gluster volume info
  Volume Name: vol2
  Type: Replicate
  Volume ID: e0332771-a3c2-4fe5-980c-b3860cfe3baf
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  Options Reconfigured:
  encryption.data-key-size: 512
  encryption.master-key: /var/lib/glusterd/vols/vol2/encryption.master-key
  performance.open-behind: off
  performance.write-behind: off
  performance.quick-read: off
  features.encryption: on
  
  # mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/var/lib/glusterd/vols/vol2/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol2

Encryption
==========

::

  # echo "test" > /mnt/glusterfs/vol2/test.txt
  # cat /mnt/glusterfs/vol2/test.txt
  test
  
  [eins]# cat /mnt/lv2/vol2/test.txt
  Zd??]K!q??tuv
  [zwei]# cat /mnt/lv2/vol2/test.txt
  Zd??]K!q??tuv
  
  # dd if=/dev/zero of=/mnt/glusterfs/vol1/test.dat bs=1 count=32
  # dd if=/dev/zero of=/mnt/glusterfs/vol2/test.dat bs=1 count=32
  
  [eins]# dd if=/dev/zero of=/tmp/test.dat bs=1 count=32
  [eins]# diff /tmp/test.dat /mnt/lv2/vol2/test.dat
  Binary files /tmp/test.dat and /mnt/lv2/vol2/test.dat differ
  
  [eins]# diff /tmp/test.dat /mnt/lv1/vol1/test.dat
  #

Performance
===========

::

  # dd if=/dev/zero of=/mnt/glusterfs/vol1/1gb.dat bs=1M count=1024
  1024+0 records in
  1024+0 records out
  1073741824 bytes (1.1 GB) copied, 18.4542 s, 58.2 MB/s
  # dd if=/dev/zero of=/mnt/glusterfs/vol2/1gb.dat bs=1M count=1024
  1024+0 records in
  1024+0 records out
  1073741824 bytes (1.1 GB) copied, 263.633 s, 4.1 MB/s

Decryption
==========

::

  # dd if=/dev/zero of=/tmp/1gb.dat bs=1M count=1024
  1024+0 records in
  1024+0 records out
  1073741824 bytes (1.1 GB) copied, 3.61505 s, 297 MB/s
  
  # diff3 /tmp/1gb.dat /mnt/glusterfs/vol1/1gb.dat /mnt/glusterfs/vol2/1gb.dat
  #

Packet
======

::

  # tcpdump -i eth0 -XX

NFS
===

::

  # mount -t nfs -o vers=3,hard,intr,nosuid localhost:/vol2 /mnt/nfs/vol2
  mount.nfs: Connection timed out


Attack
======

the same master-key
-------------------

::

  # cp /var/lib/glusterd/vols/vol2/encryption.master-key /tmp
  # mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/tmp/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt
  # diff /mnt/glusterfs/vol-crypt/test.txt /tmp/test.txt
  #

make the MK invalid when mounting
---------------------------------

::

  # openssl rand -hex 32 > /tmp/encryption.master-key
  # diff /mnt/glusterfs/vol-crypt/test.txt /tmp/test.txt
  #

invalid master-key
------------------

::

  # umount /mnt/glusterfs/vol-crypt
  # mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/tmp/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt
  # diff /mnt/glusterfs/vol-crypt/test.txt /tmp/test.txt
  diff: /mnt/glusterfs/vol-crypt/test.txt: Invalid argument
  
  # ls -lh /mnt/glusterfs/vol-crypt
  total 1.1G
  -rw-r--r-- 1 root root 1.0G May 18 23:31 1gb.dat
  -rw-r--r-- 1 root root   32 May 18 22:57 test.dat
  -rw-r--r-- 1 root root    5 May 18 22:55 test.txt
  
  # cp /mnt/glusterfs/vol-crypt/test.txt ~/
  cp: reading `/mnt/glusterfs/vol-crypt/test.txt': Invalid argument
  
  # ls -l ~/test.txt
  -rw-r--r-- 1 root root 0 May 19 00:38 /root/test.txt
  
  # echo "test2" > /mnt/glusterfs/vol-crypt/test2.txt
  # cat /mnt/glusterfs/vol-crypt/test2.txt
  test2
  
  # diff /mnt/glusterfs/vol-crypt/test2.txt /tmp/test2.txt
  #
  
  
  # \rm /mnt/glusterfs/vol-crypt/test.txt
  mv: reading `/mnt/glusterfs/vol-crypt/test.txt': Invalid argument
  # ls -lh /mnt/glusterfs/vol-crypt
  total 1.1G
  -rw-r--r-- 1 root root 1.0G May 18 23:31 1gb.dat
  -rw-r--r-- 1 root root    6 May 19 00:39 test2.txt
  -rw-r--r-- 1 root root   32 May 18 22:57 test.dat
  -rw-r--r-- 1 root root    5 May 18 22:55 test.txt
  # \rm /mnt/glusterfs/vol-crypt/test2.txt
  # ls -lh /mnt/glusterfs/vol-crypt
  total 1.1G
  -rw-r--r-- 1 root root 1.0G May 18 23:31 1gb.dat
  -rw-r--r-- 1 root root   32 May 18 22:57 test.dat
  -rw-r--r-- 1 root root    5 May 18 22:55 test.txt
  
  
  # mv /mnt/glusterfs/vol-crypt/test.txt /mnt/glusterfs/vol-crypt/test2.txt
  mv: cannot move `/mnt/glusterfs/vol-crypt/test.txt' to a subdirectory of itself, `/mnt/glusterfs/vol-crypt/test2.txt'
  
  
  [2014-05-19 0:44:25]# umount /mnt/glusterfs/vol-crypt
  [2014-05-19 0:44:31]# mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/var/lib/glusterd/vols/vol2/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt
  [2014-05-19 0:44:58]# ls -lh /mnt/glusterfs/vol-crypt
  total 1.1G
  -rw-r--r-- 1 root root 1.0G May 18 23:31 1gb.dat
  -rw-r--r-- 1 root root    6 May 19 00:44 test2.txt
  -rw-r--r-- 1 root root   32 May 18 22:57 test.dat
  -rw-r--r-- 1 root root    5 May 18 22:55 test.txt
  [2014-05-19 0:45:12]# cat /mnt/glusterfs/vol-crypt/test2.txt
  cat: /mnt/glusterfs/vol-crypt/test2.txt: Invalid argument
  [2014-05-19 0:45:22]# rm /mnt/glusterfs/vol-crypt/test2.txt
  mv: reading `/mnt/glusterfs/vol-crypt/test2.txt': Invalid argument
  [2014-05-19 0:45:31]# \rm /mnt/glusterfs/vol-crypt/test2.txt
  rm: cannot remove `/mnt/glusterfs/vol-crypt/test2.txt': Invalid argument


volume reset
============

::

  # gluster volume info vol2
  
  Volume Name: vol2
  Type: Replicate
  Volume ID: e0332771-a3c2-4fe5-980c-b3860cfe3baf
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  Options Reconfigured:
  encryption.data-key-size: 512
  encryption.master-key: /var/lib/glusterd/vols/vol2/encryption.master-key
  performance.open-behind: off
  performance.write-behind: off
  performance.quick-read: off
  features.encryption: on
  
  # gluster volume reset vol2
  volume reset: success: reset volume successful
  
  # gluster volume info vol2
  
  Volume Name: vol2
  Type: Replicate
  Volume ID: e0332771-a3c2-4fe5-980c-b3860cfe3baf
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  
  # cat /mnt/glusterfs/vol-crypt/test2.txt
  U�%U?0��x^-�bO
  # cat /mnt/glusterfs/vol-crypt/test.txt
  Zd��]K!q�tuv


reconfigure
===========

::

  # gluster volume set vol2 encryption.master-key /var/lib/glusterd/vols/vol2/encryption.master-key
  volume set: success
  # gluster volume set vol2 encryption.data-key-size 512
  volume set: success
  # for x in quick-read write-behind open-behind; do gluster volume set vol2 performance.$x off; done
  volume set: success
  volume set: success
  volume set: success
  
  # cat /mnt/glusterfs/vol-crypt/test.txt
  Zd��]K!q�tuv
  # cat /mnt/glusterfs/vol-crypt/test2.txt
  U�%U?0��x^-�bO
  
  # umount /mnt/glusterfs/vol-crypt
  # mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/var/lib/glusterd/vols/vol2/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt
  
  # cat /mnt/glusterfs/vol-crypt/test2.txt
  U�%U?0��x^-�bO
  # cat /mnt/glusterfs/vol-crypt/test.txt
  Zd��]K!q�tuv
  
  # echo "test" > /mnt/glusterfs/vol-crypt/test.txt
  # cat /mnt/glusterfs/vol-crypt/test.txt
  test
  [eins]# cat /mnt/lv2/vol2/test.txt
  test
  
  
  [2014-05-19 0:55:24]# gluster volume stop vol2
  Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
  volume stop: vol2: success
  [2014-05-19 0:56:30]# gluster volume start vol2
  volume start: vol2: success
  [2014-05-19 0:56:39]# gluster volume info vol2
  
  Volume Name: vol2
  Type: Replicate
  Volume ID: e0332771-a3c2-4fe5-980c-b3860cfe3baf
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  [2014-05-19 0:56:42]# umount /mnt/glusterfs/vol-crypt
  [2014-05-19 0:56:54]# mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/var/lib/glusterd/vols/vol2/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt
  [2014-05-19 0:56:57]# cat /mnt/glusterfs/vol-crypt/test.txt
  test


recreate
========

::

  [2014-05-19 1:00:53]# gluster volume stop vol2
  Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
  volume stop: vol2: success
  [2014-05-19 1:01:07]# gluster volume delete vol2
  Deleting volume will erase all information about the volume. Do you want to continue? (y/n) y
  volume delete: vol2: success
  [2014-05-19 1:01:18]# brick="/mnt/lv2/vol2"; gluster volume create vol2 replica 2 eins:$brick zwei:$brick
  volume create: vol2: failed: Staging failed on zwei. Error: /mnt/lv2/vol2 is already part of a volume
  Staging failed on eins. Error: /mnt/lv2/vol2 is already part of a volume
  
  [eins]# \rm -rf /mnt/lv2/vol2
  [zwei]# \rm -rf /mnt/lv2/vol2
  # brick="/mnt/lv2/vol2"; gluster volume create vol2 replica 2 eins:$brick zwei:$brick root@sieben:/root
  volume create: vol2: success: please start the volume to access data
  
  
  [2014-05-19 1:07:27]# gluster volume stop vol2
  Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
  volume stop: vol2: success
  [2014-05-19 1:07:48]# gluster volume start vol2
  volume start: vol2: success
  [2014-05-19 1:07:56]# gluster volume info vol2
  
  [2014-05-19 1:10:19]# gluster volume info vol2
  
  Volume Name: vol2
  Type: Replicate
  Volume ID: a698e3bd-a9ea-46ff-9560-c9f91e84155d
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  Options Reconfigured:
  features.encryption: on
  encryption.data-key-size: 512
  encryption.master-key: /var/lib/glusterd/vols/vol2/encryption.master-key
  performance.open-behind: off
  performance.write-behind: off
  performance.quick-read: off
  
  
  
  [2014-05-19 1:14:24]# gluster volume stop vol2
  Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
  volume stop: vol2: success
  [2014-05-19 1:15:37]# gluster volume delete vol2
  Deleting volume will erase all information about the volume. Do you want to continue? (y/n) y
  volume delete: vol2: success
  [2014-05-19 1:15:43]# brick="/mnt/lv2/vol2"; gluster volume create vol2 replica 2 eins:$brick zwei:$brick root@sieben:/root
  volume create: vol2: success: please start the volume to access data
  [2014-05-19 1:16:44]# gluster volume start vol2
  volume start: vol2: success
  [2014-05-19 1:17:04]# gluster volume set vol2 encryption on
  volume set: success
  [2014-05-19 1:17:12]# for x in quick-read write-behind open-behind; do gluster volume set vol2 performance.$x off; done
  volume set: success
  volume set: success
  volume set: success
  [2014-05-19 1:17:25]# gluster volume set vol2 encryption.master-key /var/lib/glusterd/vols/vol2/encryption.master-key
  volume set: success
  [2014-05-19 1:17:32]# gluster volume set vol2 encryption.data-key-size 512
  volume set: success
  [2014-05-19 1:17:47]# mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/var/lib/glusterd/vols/vol2/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt
  Mount failed. Please check the log file for more details.
  [2014-05-19 1:17:59]# gluster volume info vol2
  
  Volume Name: vol2
  Type: Replicate
  Volume ID: d0517697-5372-44a1-960f-6db0d988f3b2
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv2/vol2
  Brick2: zwei:/mnt/lv2/vol2
  Options Reconfigured:
  encryption.data-key-size: 512
  encryption.master-key: /var/lib/glusterd/vols/vol2/encryption.master-key
  performance.open-behind: off
  performance.write-behind: off
  performance.quick-read: off
  features.encryption: on
  [2014-05-19 1:18:35]# openssl rand -hex 32 > /var/lib/glusterd/vols/vol2/encryption.master-key
  [2014-05-19 1:19:17]# gluster volume stop vol2; gluster volume start vol2
  Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
  volume stop: vol2: success
  volume start: vol2: success
  [2014-05-19 1:19:35]# mount -t glusterfs -o xlator-option=vol2-crypt.master-key=/var/lib/glusterd/vols/vol2/encryption.master-key localhost:/vol2 /mnt/glusterfs/vol-crypt


