--------------------
AFR_CLI_enhancements
--------------------

Improved logging with more clarity and statistical information. Additional clarity in logging has been on the wish list for the Gluster community for some time. This improvement addresses eight different bugzilla issues in one fell swoop. It allows visibility into why a self-heal process was initiated and which files are affected, for example. Prior to this enhancement, clearly identifying split-brain issues from the logs was often difficult for an end user or administrator, and there was no facility to identify which files were affected by a split brain issue automatically. Remediating split brain without quorum will still require some manual effort, but with the tools provided this will become much simpler.


