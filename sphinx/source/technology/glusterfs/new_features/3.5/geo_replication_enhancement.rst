---------------------------
Geo-Replication Enhancement
---------------------------

A massive rewrite of the existing geo-replication architecture, this set of enhancements brings geo-replication to an entirely new level. Previously, the geo-replication process, gsyncd, was a single point of failure as it only ran on one node in the cluster. If the node running gsyncd failed, the entire geo-replication process was offline until the issue was addressed. The original geo-rep was a vast improvement over plain rsync checksumming and made intelligent use of xattrs to identify a reduced list of candidates for block- or file-level copy, massively improving on full directory crawls performed by rsync. In this latest incarnation, the improvement is extended even further by foregoing use of xattrs to identify change candidates and directly consuming from the changelog, which will improve performance twofold: one, by keeping a running list of only those files that may need to be synced; and two, the changelog is maintained in memory, which will allow near instant access to which data needs to be changed and where by the gsync daemon.

