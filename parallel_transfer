#!/bin/bash

: '
This bash script automatize the data transfer from one remote server to
a local host. The transfering is make in a parallel fashion, since usually
the internet rate transfer is the bottleneck.

DEPENDENCIES: GNU parallel and rsync

Before using this script, make sure you have a SSH passwordless login.
INSTRUCTIONS:
1) Generate authentication keys on your local machine:
   $ ssh-keygen
2) Copy public key to the remote server:
   $ ssh-copy-id -i ~/.ssh/id_rsa.pub UserName@RemoteServer
   (ssh-copy-id connects to the remote machine and install your public key by
   adding it the authorized_keys file.)
3) Add a private key to the authentication agent on the local server:
   $ ssh-add

Author: Renato Fuzaro Miotto              Date: Aug 04 2019
'

################################################################################
#                                    INPUTS                                    #
################################################################################


remote_host="user@remote.server"
remote_dir="/home/miotto/test"
local_dir="/home/miotto/Desktop/test"

ignored_files=(*.o *.mod *.a *.h *.f90 *.dat *.exe *.inp *.log)

nthreads=4


################################################################################

ignored_files=($(printf "\'%s\' " "${ignored_files[@]}")) # add single quote around each element
ignored_files=("${ignored_files[@]/#/'! -name '}") # add `! name` in front of each element

selected_files=($(ssh -q $remote_host "cd $remote_dir ; find * -type f ${ignored_files[@]} -exec ls {} + "))

printf '%s\n' "${selected_files[@]}" > transfer.log

num_files_to_be_transfered=($(wc -l transfer.log))

rm -f part.*
split -l $(echo "${num_files_to_be_transfered}/${nthreads}" | bc) transfer.log part.
rm -f transfer.log

flags="--safe-links --human-readable --progress -avv --remove-source-files"

ls part.* | parallel --will-cite --line-buffer --verbose -j ${nthreads} rsync --progress -avv --remove-source-files --files-from {} ${remote_host}:${remote_dir} ${local_dir}

rm -f part.*
