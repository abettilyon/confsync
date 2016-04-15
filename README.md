Confsync was Written by Allen Bettilyon <allen@bettilyon.net>.
Copyright Allen Bettilyon 

-------------------------------------------------------------------------------

Confsync is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

Confsync is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

For a copy of the GNU General Public License, see http://www.gnu.org/licenses
or, write to the Free Software Foundation, Inc. 

   59 Temple Place 
   Suite 330 
   Boston, MA 02111-1307  USA 

--------------------------------------------------------------------------------

Confsync is a Sysatem Administration tool that is used for keeping a local systems configuration
in sync with a master tree.  It is best suited for environments that have many machines with 
similar configurations.  Confsync is based on the concept of host classes in which there are 
certain classes of machines that carry very similar configuration.  The use of this tool allows 
a system administration team to more easily ensure consistancy accross they're environment, as
well as makes it easier to revision control the system configuration files accross the environemnt.

To get started, you must first create your master tree.  The master tree is organised hierachically,
allowing for multiple host classes to be maintained accross the same tree.  It also has the ability
to define hostname specific files.  The tree has three layers:

	1. Common
	2. Host Class
	3. Host Name

Each layer is relative to a machines / (root) file system.  For example, the following might be a
valid master tree layout:

        /mnt/master_tree
                     |
                     +-- /common/
                     |       +-- /etc/
                     |             +-- resolve.conf
                     |             +-- hosts
                     |             +-- ntpd.conf
                     |             +-- ntpd.conf..cmd
                     |             +-- snmpd.conf
                     |             +-- snmpd.conf..cmd
                     |
                     +-- /class1/
                     |       +-- /etc/
                     |       |     +-- hosts
                     |       |     +-- snmpd.conf
                     |       |     +-- snmpd.conf..cmd
                     |       +-- /var
                     |             +-- /spool
                     |                    +-- /cron
                     |                           +-- /tabs
                     |                                  +-- /root
                     |                                  +-- /root..cmd
                     |
                     +-- /class2/
                     |        +-- /etc/
                     |              +-- nptd.conf
                     |              +-- ntpd.conf..cmd
                     |              +-- snmpd.conf
                     |              +-- snmpd.conf..cmd
                     |
                     +-- /class2.host1/
                               +-- /etc/
                                    +-- snmpd.conf
                                    +-- snmpd.conf..cmd



This master tree can be presented to the clinets in many different ways.  A read-only NFS export is probalby the
easiest way to accomplish this, and it prevents a machine from accidentally modifying the master node.  You could
also use somthing like rsync to replicate the file tree out to each clinetnode.  Having a master tree source makes 
it easy to keep the configuration files under revision control.  The entire master directory structure can be kept under
CVS or Subversion to allow for a nice auditable changelog.

Confsync is called with a class definiation on the command line:  (confsync -C class1).  Confsync will walk the 
master tree and determine which files need to be copied locally.  It will take the most specific definition.  For example,

    If host1 calls:  confsync -C class2
    then that the following files would be checked against the local file systen: 

     /mnt/master_tree/common/etc/resolve.conf
     /mnt/master_tree/common/etc/hosts
     /mnt/master_tree/class2/etc/ntpd.conf
     /mnt/master_tree/class2.host1/etc/snmpd.conf

    If host1 calss: confsync -C class1:

     /mnt/master_tree/common/etc/resolve.conf
     /mnt/master_tree/class1/etc/hosts
     /mnt/master_tree/class1/etc/ntpd.conf
     /mnt/master_tree/class1/var/spool/cron/tabs/root


The ..cmd and ..pre files are pre and post commands.  If confsync determines that a particular file needs to be
copied the it will check for the existance of the ..cmd and ..pre files.  If they exist, confsync will execute them
before (..pre), and after (..cmd) copying the file.  This makes it possible to do things like restart or HUP
daemon processes when they're configuration has changed.  Make sure  the ..pre and ..cmd files have the execute 
permission set, otherwise, they will not correclty run.


confsync-sanity is an additional tool that calls confsync intelligently based on regular expression matches of
the hostname.  You'll need to modify the %class_config hash appropriately for your envrionment.  With a properly 
configured confsync-sanity script, you can veryify a local client with a simple command:

 	confsync-sanity

And force consistancy with another simple command:

 	confsync-sanity --for-real


confsync-diff is another tool that requires confsync-sanity to be correct.  It will show you diffs between all files 
in the master tree that need to be copied and they're counterpart running on the local clinet. 


Feel free to contact me with any question, commnents, suggestions or concerns about confsync.  I can be reached
at <allen@bettilyon.net>, and would love to hear about any success or horror stories of your use of Confsync, or
other System Automation tools.








  



