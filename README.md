# remote_op
Script to handle parallel data transfer from a remote server to a local host.

I have created this sript to easely download the results of my simulations from a remote cluster to my local machine.
Here, I use GNU Parallel (http://dx.doi.org/10.5281/zenodo.16303) to transfer data using multiple threads and rsync (http://rsync.samba.org/) to find files that need to be transferred, that is, files that have changed in size or in last-modified time.

Here I decided to go for a ssh to list the files to be downloaded, but it is also possible to run rsync using the --dry-run flag to see what files will be affected.
