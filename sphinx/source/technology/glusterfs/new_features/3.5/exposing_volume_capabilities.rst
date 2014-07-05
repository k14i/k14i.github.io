----------------------------
Exposing Volume Capabilities
----------------------------

Provides client-side insight into whether a volume is using the BD translator and, if so, which capabilities are being utilized.


::
  GlusterFS configure summary
  ===========================
  FUSE client          : yes
  Infiniband verbs     : no
  epoll IO multiplex   : yes
  argp-standalone      : no
  fusermount           : yes
  readline             : yes
  georeplication       : yes
  Linux-AIO            : yes
  Enable Debug         : yes
  systemtap            : yes
  Block Device xlator  : yes
  glupy                : yes
  Use syslog           : yes
  XML output           : no
  QEMU Block formats   : yes
  Encryption xlator    : yes
  
  
  brick="/mnt/bd0-meta/0?vg0"; gluster volume create bd0 eins:${brick} zwei:${brick} drei:${brick} vier:${brick} fuenf:${brick} sechs:${brick}
  
  gluster volume create bd sieben:/bd-meta\?vg0 force
  gluster volume start bd
  
  [2014-05-18 11:34:46]# gluster volume info bd
  
  Volume Name: bd
  Type: Distribute
  Volume ID: b036242b-9469-4873-aed6-f264d0c66fea
  Status: Started
  Xlator 1: BD
  Capability 1: offload_copy
  Capability 2: offload_snapshot
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: sieben:/bd-meta
  Brick1 VG: vg0
  
  
  gluster volume create bd sieben:/tmp/bd-meta\?bd-vg
  gluster volume start bd
  
  [2014-05-18 12:58:59]# gluster volume info bd
  
  Volume Name: bd
  Type: Distribute
  Volume ID: 5020ac7a-ad66-490c-bd00-d49b8ce8b54a
  Status: Started
  Xlator 1: BD
  Capability 1: offload_copy
  Capability 2: offload_snapshot
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: sieben:/tmp/bd-meta
  Brick1 VG: bd-vg
  
  
  [2014-05-18 13:24:51]# touch /mnt/glusterfs/bd/lv0
  [2014-05-18 13:25:05]# setfattr -n "user.glusterfs.bd" -v "lv:64MB" /mnt/glusterfs/bd/lv0
  
  
  [2014-05-18 13:25:34]# lvdisplay bd-vg
    --- Logical volume ---
    LV Path                /dev/bd-vg/7eef4708-88aa-411d-8d8d-1dfd2b111a2a
    LV Name                7eef4708-88aa-411d-8d8d-1dfd2b111a2a
    VG Name                bd-vg
    LV UUID                lxdeS7-nHva-AMsI-ymdx-gveE-Qyzz-2Xm5Wu
    LV Write Access        read/write
    LV Creation host, time sieben.infinibridge.net, 2014-05-18 13:25:34 +0900
    LV Status              available
    # open                 0
    LV Size                64.00 MiB
    Current LE             16
    Segments               1
    Allocation             inherit
    Read ahead sectors     0
    Block device           253:5
  
  
  [2014-05-18 13:27:40]# ls -lh /mnt/glusterfs/bd/
  合計 64M
  -rw-r--r-- 1 root root 64M  5月 18 13:25 2014 lv0
  
  truncate /mnt/glusterfs/bd/lv0 -s 4000M
  
  
  [2014-05-18 13:52:52]# lvdisplay bd-vg
    --- Logical volume ---
    LV Path                /dev/bd-vg/3111882a-bfdd-4ea5-8185-ef31c3984b10
    LV Name                3111882a-bfdd-4ea5-8185-ef31c3984b10
    VG Name                bd-vg
    LV UUID                vp3C45-GmHK-e0Nr-JZmF-pbTI-mr3O-y7gT4b
    LV Write Access        read/write
    LV Creation host, time sieben.infinibridge.net, 2014-05-18 13:51:37 +0900
    LV Status              available
    # open                 0
    LV Size                3.91 GiB
    Current LE             1000
    Segments               1
    Allocation             inherit
    Read ahead sectors     0
    Block device           253:5
  
  [2014-05-18 13:52:59]# ls -lh /mnt/glusterfs/bd/lv0
  -rw-r--r-- 1 root root 4.0G  5月 18 13:51 2014 /mnt/glusterfs/bd/lv0
  
  ----
  
  [2014-05-18 13:59:33]# losetup /dev/loop6 /tmp/bd-loop6
  [2014-05-18 14:01:54]# pvcreate /dev/loop6
  
  [2014-05-18 14:21:01]# vgcreate bd0-vg /dev/loop6
    Volume group "bd0-vg" successfully created
  [2014-05-18 14:21:26]# lvcreate --thin bd0-vg -L 1000M
    Logical volume "lvol0" created
    Logical volume "lvol1" created
  [2014-05-18 14:21:42]# lvdisplay bd0-vg
    --- Logical volume ---
    LV Name                lvol1
    VG Name                bd0-vg
    LV UUID                xxU6HJ-TBXd-WFaM-GoQK-z0pB-eInt-HouO15
    LV Write Access        read/write
    LV Creation host, time sieben.infinibridge.net, 2014-05-18 14:21:42 +0900
    LV Pool transaction ID 0
    LV Pool metadata       lvol1_tmeta
    LV Pool data           lvol1_tdata
    LV Pool chunk size     64.00 KiB
    LV Zero new blocks     yes
    LV Status              available
    # open                 0
    LV Size                1000.00 MiB
    Allocated pool data    0.00%
    Allocated metadata     0.88%
    Current LE             250
    Segments               1
    Allocation             inherit
    Read ahead sectors     auto
    - currently set to     256
    Block device           253:5
  
  
  [2014-05-18 14:23:30]# mkdir /tmp/bd0-meta
  [2014-05-18 14:23:33]# gluster volume create bd0 sieben:/tmp/bd0-meta\?bd0-vg force
  volume create: bd0: success: please start the volume to access data
  [2014-05-18 14:23:46]# gluster volume start bd0
  [2014-05-18 14:02:50]# mkdir -p /tmp/bd0-meta
  volume start: bd0: success
  [2014-05-18 14:24:03]# gluster volume info bd0
  
  Volume Name: bd0
  Type: Distribute
  Volume ID: 0d8dedfd-af1e-4ffa-94a6-478d428d3913
  Status: Started
  Xlator 1: BD
  Capability 1: thin
  Capability 2: offload_copy
  Capability 3: offload_snapshot
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: sieben:/tmp/bd0-meta
  Brick1 VG: bd0-vg
  
  ----
  
  
  [2014-05-18 14:37:04]# losetup /dev/loop6 /tmp/bd-loop6
  [2014-05-18 14:37:41]# pvcreate /dev/loop6
    Physical volume "/dev/loop6" successfully created
  [2014-05-18 14:37:49]# vgcreate bd0-vg /dev/loop6
    Volume group "bd0-vg" successfully created
  [2014-05-18 14:38:09]# lvcreate --thin bd0-vg -L 1000M
    Logical volume "lvol0" created
    Logical volume "lvol1" created
  [2014-05-18 14:38:21]# lvdisplay bd0-vg
    --- Logical volume ---
    LV Name                lvol1
    VG Name                bd0-vg
    LV UUID                PSAFkr-Vyr8-fkGU-kDnA-rWUF-fFFT-111Snr
    LV Write Access        read/write
    LV Creation host, time sieben.infinibridge.net, 2014-05-18 14:38:21 +0900
    LV Pool transaction ID 0
    LV Pool metadata       lvol1_tmeta
    LV Pool data           lvol1_tdata
    LV Pool chunk size     64.00 KiB
    LV Zero new blocks     yes
    LV Status              available
    # open                 0
    LV Size                1000.00 MiB
    Allocated pool data    0.00%
    Allocated metadata     0.88%
    Current LE             250
    Segments               1
    Allocation             inherit
    Read ahead sectors     auto
    - currently set to     256
    Block device           253:5
  
  [2014-05-18 14:38:33]# mkdir /tmp/bd0-meta
  [2014-05-18 14:39:34]# gluster volume create bd0 sieben:/tmp/bd0-meta\?bd0-vg force
  volume create: bd0: success: please start the volume to access data
  
  [2014-05-18 14:40:09]# gluster volume start bd0
  volume start: bd0: success
  [2014-05-18 14:42:46]# gluster volume info bd0
  
  Volume Name: bd0
  Type: Distribute
  Volume ID: 019d0f4b-d11a-480e-9be8-0c79902f0746
  Status: Started
  Xlator 1: BD
  Capability 1: thin
  Capability 2: offload_copy
  Capability 3: offload_snapshot
  Number of Bricks: 1
  Transport-type: tcp
  Bricks:
  Brick1: sieben:/tmp/bd0-meta
  Brick1 VG: bd0-vg
  
  [2014-05-18 14:43:30]# mkdir /mnt/glusterfs/bd0
  [2014-05-18 14:43:35]# mount -t glusterfs sieben:/bd0 /mnt/glusterfs/bd0
  
  setfattr -n "user.glusterfs.bd" -v "thin:1024MB" /mnt/glusterfs/bd0/lv0
  
  
  [2014-05-18 14:47:31]# lvdisplay bd0-vg
    --- Logical volume ---
    LV Name                lvol1
    VG Name                bd0-vg
    LV UUID                PSAFkr-Vyr8-fkGU-kDnA-rWUF-fFFT-111Snr
    LV Write Access        read/write
    LV Creation host, time sieben.infinibridge.net, 2014-05-18 14:38:21 +0900
    LV Pool transaction ID 1
    LV Pool metadata       lvol1_tmeta
    LV Pool data           lvol1_tdata
    LV Pool chunk size     64.00 KiB
    LV Zero new blocks     yes
    LV Status              available
    # open                 0
    LV Size                1000.00 MiB
    Allocated pool data    0.00%
    Allocated metadata     0.98%
    Current LE             250
    Segments               1
    Allocation             inherit
    Read ahead sectors     auto
    - currently set to     256
    Block device           253:5
  
    --- Logical volume ---
    LV Path                /dev/bd0-vg/a9790eba-ffbf-4d9c-a674-e02c61ece935
    LV Name                a9790eba-ffbf-4d9c-a674-e02c61ece935
    VG Name                bd0-vg
    LV UUID                Z4HtWM-W0jk-YiK5-66ED-zOMw-YhFp-nrnRUU
    LV Write Access        read/write
    LV Creation host, time sieben.infinibridge.net, 2014-05-18 14:47:31 +0900
    LV Pool name           lvol1
    LV Status              available
    # open                 0
    LV Size                1.00 GiB
    Mapped size            0.00%
    Current LE             256
    Segments               1
    Allocation             inherit
    Read ahead sectors     auto
    - currently set to     256
    Block device           253:9
  
  
  
  [2014-05-18 14:54:57]# lvdisplay -C bd0-vg
    LV                                   VG     Attr       LSize    Pool  Origin Data%  Move Log Cpy%Sync Convert
    39b82644-f8ef-435d-b14e-d199a7e264fa bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    6002ddb2-28f1-463c-8666-f683fe2441ed bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    69993340-d691-4502-a9d5-375b8be0fb9e bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    82af50a2-0124-41d8-a887-d8c30427a663 bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    996969dd-3e32-491b-95d1-f279e6808d5b bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    a9790eba-ffbf-4d9c-a674-e02c61ece935 bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    d6fd964a-67f8-4d48-96d1-343bed4ee792 bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    ea58b011-3a41-4bf0-9fe6-3862e24b86f6 bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    f7df48e5-09b1-4314-b729-1f38e5ceec2e bd0-vg Vwi-a-tz--    1.00g lvol1          0.00
    lvol1                                bd0-vg twi-a-tz-- 1000.00m                0.00
  
  
  
  [2014-05-18 14:57:51]# ls -lh /mnt/glusterfs/bd0
  合計 10G
  -rw-r--r-- 1 root root 1.0G  5月 18 14:46 2014 lv0
  -rw-r--r-- 1 root root 1.0G  5月 18 14:50 2014 lv1
  -rw-r--r-- 1 root root 1.0G  5月 18 14:51 2014 lv2
  -rw-r--r-- 1 root root 1.0G  5月 18 14:51 2014 lv3
  -rw-r--r-- 1 root root 1.0G  5月 18 14:52 2014 lv4
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv5
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv6
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv7
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv8
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv9
  
  
  [2014-05-18 15:05:23]# mkdir /mnt/bd0-lv/{39b82644-f8ef-435d-b14e-d199a7e264fa,6002ddb2-28f1-463c-8666-f683fe2441ed,69993340-d691-4502-a9d5-375b8be0fb9e,82af50a2-0124-41d8-a887-d8c30427a663,996969dd-3e32-491b-95d1-f279e6808d5b,a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e,a9790eba-ffbf-4d9c-a674-e02c61ece935,d6fd964a-67f8-4d48-96d1-343bed4ee792,ea58b011-3a41-4bf0-9fe6-3862e24b86f6,f7df48e5-09b1-4314-b729-1f38e5ceec2e}
  [2014-05-18 15:06:37]# ls /mnt/bd0-lv
  39b82644-f8ef-435d-b14e-d199a7e264fa  a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e
  6002ddb2-28f1-463c-8666-f683fe2441ed  a9790eba-ffbf-4d9c-a674-e02c61ece935
  69993340-d691-4502-a9d5-375b8be0fb9e  d6fd964a-67f8-4d48-96d1-343bed4ee792
  82af50a2-0124-41d8-a887-d8c30427a663  ea58b011-3a41-4bf0-9fe6-3862e24b86f6
  996969dd-3e32-491b-95d1-f279e6808d5b  f7df48e5-09b1-4314-b729-1f38e5ceec2e
  
  
  
  [2014-05-18 15:08:20]# for x in 39b82644-f8ef-435d-b14e-d199a7e264fa 6002ddb2-28f1-463c-8666-f683fe2441ed 69993340-d691-4502-a9d5-375b8be0fb9e 82af50a2-0124-41d8-a887-d8c30427a663 996969dd-3e32-491b-95d1-f279e6808d5b a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e a9790eba-ffbf-4d9c-a674-e02c61ece935 d6fd964a-67f8-4d48-96d1-343bed4ee792 ea58b011-3a41-4bf0-9fe6-3862e24b86f6 f7df48e5-09b1-4314-b729-1f38e5ceec2e; do mkfs.xfs -i size=512 /dev/bd0-vg/$x && mount -t xfs /dev/bd0-vg/$x /mnt/bd0-lv/$x; done
  meta-data=/dev/bd0-vg/39b82644-f8ef-435d-b14e-d199a7e264fa isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/6002ddb2-28f1-463c-8666-f683fe2441ed isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/69993340-d691-4502-a9d5-375b8be0fb9e isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/82af50a2-0124-41d8-a887-d8c30427a663 isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/996969dd-3e32-491b-95d1-f279e6808d5b isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/a9790eba-ffbf-4d9c-a674-e02c61ece935 isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/d6fd964a-67f8-4d48-96d1-343bed4ee792 isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/ea58b011-3a41-4bf0-9fe6-3862e24b86f6 isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  meta-data=/dev/bd0-vg/f7df48e5-09b1-4314-b729-1f38e5ceec2e isize=512    agcount=8, agsize=32768 blks
           =                       sectsz=512   attr=2, projid32bit=0
  data     =                       bsize=4096   blocks=262144, imaxpct=25
           =                       sunit=0      swidth=0 blks
  naming   =version 2              bsize=4096   ascii-ci=0
  log      =internal log           bsize=4096   blocks=2560, version=2
           =                       sectsz=512   sunit=0 blks, lazy-count=1
  realtime =none                   extsz=4096   blocks=0, rtextents=0
  
  
  
  [2014-05-18 15:10:12]# df -h | grep bd0-lv
  /dev/dm-13           1014M   33M  982M   4% /mnt/bd0-lv/39b82644-f8ef-435d-b14e-d199a7e264fa
  /dev/dm-16           1014M   33M  982M   4% /mnt/bd0-lv/6002ddb2-28f1-463c-8666-f683fe2441ed
  /dev/dm-18           1014M   33M  982M   4% /mnt/bd0-lv/69993340-d691-4502-a9d5-375b8be0fb9e
  /dev/dm-11           1014M   33M  982M   4% /mnt/bd0-lv/82af50a2-0124-41d8-a887-d8c30427a663
  /dev/dm-12           1014M   33M  982M   4% /mnt/bd0-lv/996969dd-3e32-491b-95d1-f279e6808d5b
  /dev/dm-17           1014M   33M  982M   4% /mnt/bd0-lv/a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e
  /dev/dm-9            1014M   33M  982M   4% /mnt/bd0-lv/a9790eba-ffbf-4d9c-a674-e02c61ece935
  /dev/dm-14           1014M   33M  982M   4% /mnt/bd0-lv/d6fd964a-67f8-4d48-96d1-343bed4ee792
  /dev/dm-15           1014M   33M  982M   4% /mnt/bd0-lv/ea58b011-3a41-4bf0-9fe6-3862e24b86f6
  /dev/dm-10           1014M   33M  982M   4% /mnt/bd0-lv/f7df48e5-09b1-4314-b729-1f38e5ceec2e
  
  
  
  [2014-05-18 15:10:54]# mount | grep bd0-lv
  /dev/mapper/bd0--vg-39b82644--f8ef--435d--b14e--d199a7e264fa on /mnt/bd0-lv/39b82644-f8ef-435d-b14e-d199a7e264fa type xfs (rw)
  /dev/mapper/bd0--vg-6002ddb2--28f1--463c--8666--f683fe2441ed on /mnt/bd0-lv/6002ddb2-28f1-463c-8666-f683fe2441ed type xfs (rw)
  /dev/mapper/bd0--vg-69993340--d691--4502--a9d5--375b8be0fb9e on /mnt/bd0-lv/69993340-d691-4502-a9d5-375b8be0fb9e type xfs (rw)
  /dev/mapper/bd0--vg-82af50a2--0124--41d8--a887--d8c30427a663 on /mnt/bd0-lv/82af50a2-0124-41d8-a887-d8c30427a663 type xfs (rw)
  /dev/mapper/bd0--vg-996969dd--3e32--491b--95d1--f279e6808d5b on /mnt/bd0-lv/996969dd-3e32-491b-95d1-f279e6808d5b type xfs (rw)
  /dev/mapper/bd0--vg-a19ac2af--94df--4d01--b7c3--bbfcbfe5d09e on /mnt/bd0-lv/a19ac2af-94df-4d01-b7c3-bbfcbfe5d09e type xfs (rw)
  /dev/mapper/bd0--vg-a9790eba--ffbf--4d9c--a674--e02c61ece935 on /mnt/bd0-lv/a9790eba-ffbf-4d9c-a674-e02c61ece935 type xfs (rw)
  /dev/mapper/bd0--vg-d6fd964a--67f8--4d48--96d1--343bed4ee792 on /mnt/bd0-lv/d6fd964a-67f8-4d48-96d1-343bed4ee792 type xfs (rw)
  /dev/mapper/bd0--vg-ea58b011--3a41--4bf0--9fe6--3862e24b86f6 on /mnt/bd0-lv/ea58b011-3a41-4bf0-9fe6-3862e24b86f6 type xfs (rw)
  /dev/mapper/bd0--vg-f7df48e5--09b1--4314--b729--1f38e5ceec2e on /mnt/bd0-lv/f7df48e5-09b1-4314-b729-1f38e5ceec2e type xfs (rw)
  
  
  
  [2014-05-18 15:02:49]# mount -t glusterfs localhost:/bd0 /mnt/glusterfs/bd0
  
  [2014-05-18 15:01:47]# ls -lh /mnt/glusterfs/bd0
  合計 10G
  -rw-r--r-- 1 root root 1.0G  5月 18 14:46 2014 lv0
  -rw-r--r-- 1 root root 1.0G  5月 18 14:50 2014 lv1
  -rw-r--r-- 1 root root 1.0G  5月 18 14:51 2014 lv2
  -rw-r--r-- 1 root root 1.0G  5月 18 14:51 2014 lv3
  -rw-r--r-- 1 root root 1.0G  5月 18 14:52 2014 lv4
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv5
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv6
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv7
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv8
  -rw-r--r-- 1 root root 1.0G  5月 18 14:54 2014 lv9
  
  [2014-05-18 15:12:27]# losetup /dev/loop6 /mnt/glusterfs/bd0/lv0
  
  [2014-05-18 15:15:49]# df -h | grep bd0-lv
  /dev/loop6           1014M   33M  982M   4% /mnt/bd0-lv/lv0
  [2014-05-18 15:16:06]# mount | grep bd0-lv
  /dev/loop6 on /mnt/bd0-lv/lv0 type xfs (rw)
  
  
  
  [2014-05-18 15:00:00]# mkdir /mnt/glusterfs/bd0
  [2014-05-18 15:00:12]# mount -t glusterfs localhost:/bd0 /mnt/glusterfs/bd0
  [2014-05-18 15:20:33]# losetup /dev/loop6 /mnt/glusterfs/bd0/lv0
  
  
  [2014-05-18 15:22:37]# df -h | grep bd0-lv
  /dev/loop6           1014M   33M  982M   4% /mnt/bd0-lv/lv0
  [2014-05-18 15:22:43]# mount | grep bd0-lv
  /dev/loop6 on /mnt/bd0-lv/lv0 type xfs (rw)
  
  
  [2014-05-18 15:25:16]# mount -t xfs -o loop /mnt/glusterfs/bd0/lv1 /mnt/bd0-lv/lv1
  
  [2014-05-18 15:25:52]# df -h | grep bd0-lv
  /dev/loop6           1014M   33M  982M   4% /mnt/bd0-lv/lv0
                       1014M   33M  982M   4% /mnt/bd0-lv/lv1


