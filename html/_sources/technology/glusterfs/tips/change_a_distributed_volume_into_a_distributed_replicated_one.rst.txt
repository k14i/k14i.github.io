--------------------------------------------------------------
Distributed volume を Distributed-Replicated volume に変更する
--------------------------------------------------------------

確認バージョン: 3.5

想定環境

::

  Volume Name: vol0
  Type: Distributed
  Volume ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  Status: Started
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv0/vol0

* gluster volume stop vol0
* gluster volume delete vol0
* [eins]# rm -rf /mnt/lv0/vol0/.glusterfs
* [eins]# for xattr in gfid glusterfs.dht glusterfs.volume-id; do setfattr --remove="trusted.$xattr" /mnt/lv0/vol0; done
* gluster volume create replica 2 eins:/mnt/lv0/vol0 zwei:/mnt/lv0/vol0
* mount -t glusterfs localhost:vol0 /mnt/glusterfs/vol0
* ls -alR /mnt/glusterfs/vol0 > /dev/null
* gluster volume heal vol0

gluster volume info 結果

::

  Volume Name: vol0
  Type: Replicate
  Volume ID: yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv0/vol0
  Brick2: zwei:/mnt/lv0/vol0

