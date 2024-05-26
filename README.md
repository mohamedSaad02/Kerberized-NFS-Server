# Kerberized-NFS-Server

# Project Overview
This project consists of three Fedora server virtual machines configured to demonstrate a secure NFS (Network File System) setup with Kerberos authentication. The three virtual machines include:

NFS Server: Hosts the shared directory.
NFS Client: Accesses the shared directory hosted by the NFS Server.
Kerberos Server: Provides authentication services.

# Prerequisites
VirtualBox or any other virtualization software
Vagrant (optional, for easier VM management)
Basic knowledge of Fedora and Linux command line

# Main Goal
The idea of this project is to set a NFS environment in which the client must be authorized and have a valid entry in the kerberos server in order to be able to access the exported directory from the NFS Server.

# Project Configuration
**** A few files must be modified in order to implement the idea we talked about, first we will discuss the common configuration between the three nodes :
First of all, each hostname must be changed to a certain way : host.nfsshare.com in our case.
-/etc/hosts : This file contains all the hosts and their ip addresses, so we will add an entry for each machine containing its hostname (host.nfsshare.com) and the ip address.
-/etc/krb5.conf : This file contains the configuration of the kerberos environment, in this file we must change the (example.com and EXAMPLE.COM) with our domain name that we have used (nfsshare.com or NFSSHARE.COM). Also, in the realm that we will be using (NFSSHARE.COM) the kdc and admin_server hostname must be specified to the kerberos server defined hostname, and we have to make sure that the hosts file is changed and has the correct configurations for all machines.
**** Now we will move on to the nfs server configuration : 
-/etc/exports : in here we specifie the directory that we want to export and the ip address of the network or the host that is allowed to access it. we also specifie sec=krb5 as a security option if we want the nfs to use kerberos authentication features.
**** For the kerberos server : 
-/var/kerberos/krb5kdc/kadm.acl : here we must change example.com with our realm name (nfsshare.com)
**** As for the nfs server : 
-/etc/fstab : in this file, we specifie the exported directory that we will try and mount with the mounting directory created in the host (nfs-client).

# Crucial actions
After creating the kerberos database and creating the principals, we must add them to the keytab and each node must have a keytab that contains its principal so that the auto-authentication can be performed while trying to mount the directory, we can use ktadd -k and specifie a different file that the kerberos main keytab file to use for adding the host principal to the host keytab.
One better way to ensure that the content will be the same , is to use : scp root@host.nfsshare.com:/etc/krb5-hostname.keytab /etc/krb5.keytab. This way, the content will be exactly the same.
