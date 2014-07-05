-----------------------
Brick Failure Detection
-----------------------

Detecting failures on the filesystem that a brick uses makes it possible to handle errors that are caused from outside of the Gluster environment.

::
  [2014-05-19 3:39:53]# brick="/mnt/lv4/vol4";gluster volume create vol4 eins:$brick zwei:$brick drei:$brick vier:$brick fuenf:$brick sechs:$brick
  volume create: vol4: success: please start the volume to access data
  [2014-05-19 3:40:22]# gluster volume start vol4
  volume start: vol4: success
  
  # gluster volume set vol4 storage.health-check-interval 10
  volume set: success
  
  
  [2014-05-19 3:51:16]# gluster volume info vol4
  
  Volume Name: vol4
  Type: Distribute
  Volume ID: 706122a9-44fc-4d1d-8c3b-97482d98b95c
  Status: Started
  Number of Bricks: 6
  Transport-type: tcp
  Bricks:
  Brick1: eins:/mnt/lv4/vol4
  Brick2: zwei:/mnt/lv4/vol4
  Brick3: drei:/mnt/lv4/vol4
  Brick4: vier:/mnt/lv4/vol4
  Brick5: fuenf:/mnt/lv4/vol4
  Brick6: sechs:/mnt/lv4/vol4
  Options Reconfigured:
  storage.health-check-interval: 10
  
  
  [sechs]# dmsetup table
  vg0-swift: 0 209715200 linear 8:7 838862848
  vg0-cinder: 0 209715200 linear 8:7 419432448
  vg0-lv4: 0 209715200 linear 8:7 1468008448
  vg0-lv3: 0 209715200 linear 8:7 1258293248
  vg0-lv2: 0 209715200 linear 8:7 1048578048
  vg0-lv1: 0 209715200 linear 8:7 209717248
  vg0-lv0: 0 209715200 linear 8:7 2048
  vg0-glance: 0 209715200 linear 8:7 629147648
  
  # echo 0 209715200 error > dmsetup-error-target
  
  
  [2014-05-19 3:43:43]# dmsetup load vg0-lv4 dmsetup-error-target
  [2014-05-19 3:44:46]# dmsetup resume vg0-lv4
  [2014-05-19 3:45:06]# dmsetup table
  vg0-swift: 0 209715200 linear 8:7 838862848
  vg0-cinder: 0 209715200 linear 8:7 419432448
  vg0-lv4: 0 209715200 error
  vg0-lv3: 0 209715200 linear 8:7 1258293248
  vg0-lv2: 0 209715200 linear 8:7 1048578048
  vg0-lv1: 0 209715200 linear 8:7 209717248
  vg0-lv0: 0 209715200 linear 8:7 2048
  vg0-glance: 0 209715200 linear 8:7 629147648

brick
-----

::
  [2014-05-18 18:49:53.720594] I [glusterfsd-mgmt.c:56:mgmt_cbk_spec] 0-mgmt: Volume file changed
  [2014-05-18 18:50:04.238239] W [posix-helpers.c:1294:posix_health_check_thread_proc] 0-vol4-posix: stat() on /mnt/lv4/vol4 returned: Input/output error
  [2014-05-18 18:50:04.238328] M [posix-helpers.c:1314:posix_health_check_thread_proc] 0-vol4-posix: health-check failed, going down
  
  Message from syslogd@sechs at May 19 03:50:04 ...
   glusterfsd: [2014-05-18 18:50:04.238328] M [posix-helpers.c:1314:posix_health_check_thread_proc] 0-vol4-posix: health-check failed, going down
  
  
  [2014-05-18 18:50:34.238551] M [posix-helpers.c:1319:posix_health_check_thread_proc] 0-vol4-posix: still alive! -> SIGTERM
  
  Message from syslogd@sechs at May 19 03:50:34 ...
   glusterfsd: [2014-05-18 18:50:34.238551] M [posix-helpers.c:1319:posix_health_check_thread_proc] 0-vol4-posix: still alive! -> SIGTERM
  [2014-05-18 18:50:34.238910] W [glusterfsd.c:1095:cleanup_and_exit] (-->/lib64/libc.so.6(clone+0x6d) [0x7f1144ebab7d] (-->/lib64/libpthread.so.0(+0x79d1) [0x7f114554d9d1] (-->/usr/local/glusterfs-3.5.0/sbin/glusterfsd(glusterfs_sigwaiter+0xf0) [0x4085af]))) 0-: received signum (15), shutting down

syslog
------

::
  May 19 03:49:55 sechs kernel: XFS (dm-7): metadata I/O error: block 0x0 ("xfs_buf_iodone_callbacks") error 5 buf count 4096
  May 19 03:49:57 sechs kernel: XFS (dm-7): metadata I/O error: block 0x6400108 ("xlog_iodone") error 5 buf count 4096
  May 19 03:49:57 sechs kernel: XFS (dm-7): xfs_do_force_shutdown(0x2) called from line 1062 of file fs/xfs/xfs_log.c.  Return address = 0xffffffffa04dd131
  May 19 03:49:57 sechs kernel: XFS (dm-7): Log I/O Error Detected.  Shutting down filesystem
  May 19 03:49:57 sechs kernel: XFS (dm-7): Please umount the filesystem and rectify the problem(s)
  May 19 03:50:04 sechs glusterfsd: [2014-05-18 18:50:04.238328] M [posix-helpers.c:1314:posix_health_check_thread_proc] 0-vol4-posix: health-check failed, going down
  
  Message from syslogd@sechs at May 19 03:50:04 ...
   glusterfsd: [2014-05-18 18:50:04.238328] M [posix-helpers.c:1314:posix_health_check_thread_proc] 0-vol4-posix: health-check failed, going down
  May 19 03:50:27 sechs kernel: XFS (dm-7): xfs_log_force: error 5 returned.
  
  Message from syslogd@sechs at May 19 03:50:34 ...
   glusterfsd: [2014-05-18 18:50:34.238551] M [posix-helpers.c:1319:posix_health_check_thread_proc] 0-vol4-posix: still alive! -> SIGTERM
  May 19 03:50:34 sechs glusterfsd: [2014-05-18 18:50:34.238551] M [posix-helpers.c:1319:posix_health_check_thread_proc] 0-vol4-posix: still alive! -> SIGTERM
  May 19 03:50:57 sechs kernel: XFS (dm-7): xfs_log_force: error 5 returned.

gluster volume status
---------------------

::
  [2014-05-19 3:52:41]# gluster volume status vol4
  Status of volume: vol4
  Gluster process						Port	Online	Pid
  ------------------------------------------------------------------------------
  Brick eins:/mnt/lv4/vol4				49160	Y	2925
  Brick zwei:/mnt/lv4/vol4				49159	Y	440
  Brick drei:/mnt/lv4/vol4				49152	Y	32500
  Brick vier:/mnt/lv4/vol4				49152	Y	32657
  Brick fuenf:/mnt/lv4/vol4				49152	Y	24517
  Brick sechs:/mnt/lv4/vol4				N/A	N	N/A
  NFS Server on localhost					2049	Y	29535
  NFS Server on zwei					N/A	N	N/A
  NFS Server on vier					N/A	N	N/A
  NFS Server on drei					N/A	N	N/A
  NFS Server on eins					N/A	N	N/A
  NFS Server on fuenf					N/A	N	N/A
  NFS Server on sechs					N/A	N	N/A
  
  Task Status of Volume vol4
  ------------------------------------------------------------------------------
  There are no active volume tasks


process
-------

::
  # ps -ef | grep glusterfsd | grep -v grep | wc -l
  0

service glusterd restart
------------------------

::
  [2014-05-18 18:58:17.197872] I [glusterfsd.c:1959:main] 0-/usr/local/glusterfs-3.5.0/sbin/glusterfsd: Started running /usr/local/glusterfs-3.5.0/sbin/glusterfsd version 3.5git (/usr/local/glusterfs-3.5.0/sbin/glusterfsd -s sechs --volfile-id vol4.sechs.mnt-lv4-vol4 -p /var/lib/glusterd/vols/vol4/run/sechs-mnt-lv4-vol4.pid -S /var/run/23afc72b5ceddccd28b405b1cdf5b4df.socket --brick-name /mnt/lv4/vol4 -l /usr/local/glusterfs-3.5.0/var/log/glusterfs/bricks/mnt-lv4-vol4.log --xlator-option *-posix.glusterd-uuid=0765d288-a59b-4ccf-90ae-c3332c83dbf4 --brick-port 49152 --xlator-option vol4-server.listen-port=49152)
  [2014-05-18 18:58:17.205310] I [socket.c:3561:socket_init] 0-socket.glusterfsd: SSL support is NOT enabled
  [2014-05-18 18:58:17.205486] I [socket.c:3576:socket_init] 0-socket.glusterfsd: using system polling thread
  [2014-05-18 18:58:17.205880] I [socket.c:3561:socket_init] 0-glusterfs: SSL support is NOT enabled
  [2014-05-18 18:58:17.205949] I [socket.c:3576:socket_init] 0-glusterfs: using system polling thread
  [2014-05-18 18:58:18.834910] I [graph.c:254:gf_add_cmdline_options] 0-vol4-server: adding option 'listen-port' for volume 'vol4-server' with value '49152'
  [2014-05-18 18:58:18.834976] I [graph.c:254:gf_add_cmdline_options] 0-vol4-posix: adding option 'glusterd-uuid' for volume 'vol4-posix' with value '0765d288-a59b-4ccf-90ae-c3332c83dbf4'
  [2014-05-18 18:58:18.837332] I [rpcsvc.c:2064:rpcsvc_set_outstanding_rpc_limit] 0-rpc-service: Configured rpc.outstanding-rpc-limit with value 64
  [2014-05-18 18:58:18.837510] W [options.c:848:xl_opt_validate] 0-vol4-server: option 'listen-port' is deprecated, preferred is 'transport.socket.listen-port', continuing with correction
  [2014-05-18 18:58:18.837572] I [socket.c:3561:socket_init] 0-tcp.vol4-server: SSL support is NOT enabled
  [2014-05-18 18:58:18.837601] I [socket.c:3576:socket_init] 0-tcp.vol4-server: using system polling thread
  [2014-05-18 18:58:18.838445] E [common-utils.c:93:mkdir_p] 0-: Failed due to reason Input/output error
  [2014-05-18 18:58:18.838505] I [mem-pool.c:539:mem_pool_destroy] 0-vol4-changelog: size=108 max=0 total=0
  [2014-05-18 18:58:18.838533] E [xlator.c:403:xlator_init] 0-vol4-changelog: Initialization of volume 'vol4-changelog' failed, review your volfile again
  [2014-05-18 18:58:18.838561] E [graph.c:307:glusterfs_graph_init] 0-vol4-changelog: initializing translator failed
  [2014-05-18 18:58:18.838610] E [graph.c:502:glusterfs_graph_activate] 0-graph: init failed
  [2014-05-18 18:58:18.839480] W [glusterfsd.c:1095:cleanup_and_exit] (-->/usr/local/glusterfs-3.5.0/lib/libgfrpc.so.0(rpc_clnt_handle_reply+0x1b5) [0x7f2981c837d8] (-->/usr/local/glusterfs-3.5.0/sbin/glusterfsd(mgmt_getspec_cbk+0x36a) [0x40cf77] (-->/usr/local/glusterfs-3.5.0/sbin/glusterfsd(glusterfs_process_volfp+0x18a) [0x408bf2]))) 0-: received signum (0), shutting down


