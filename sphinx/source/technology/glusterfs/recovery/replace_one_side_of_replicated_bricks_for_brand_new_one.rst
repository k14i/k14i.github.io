-------------------------------------------
レプリカを構成するbrickの片方を新品交換する
-------------------------------------------

確認バージョン: 3.5

新品交換し、GlusterFSをインストールした後の手順。

想定環境

::

  Volume Name: vol0
  Type: Replicate
  Volume ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv0/vol0
  Brick2: zwei:/mnt/lv0/vol0

* gluster volume sync eins vol0
* mount -t glusterfs localhost:vol0 /mnt/glusterfs/vol0
* ls -alR /mnt/glusterfs/vol0 > /dev/null
* gluster volume heal vol0

gluster volume info 結果

::

  Volume Name: vol0
  Type: Replicate
  Volume ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  Status: Started
  Number of Bricks: 1 x 2 = 2
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv0/vol0
  Brick2: zwei:/mnt/lv0/vol0

