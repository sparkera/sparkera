---
layout: post
title: Adding or Removing Hadoop Nodes
category: Blog
guid: urn:uuid:0ccb922f-1ea4-4916-ae5e-20130502
tag: hadoop
---
I am here to give step completely. I saw some version of step before, but many of them either are not complete or confused or wrong, e.g. someone even stop the cluster to do that!
#### Adding Nodes 
* In the candidate nodes to add, update the host name in `"/etc/hostname"` to following the name standard in the cluster and also set proper DNS mapping in `"/etc/hosts"` for assigned IP address in network. 
* In the candidate nodes to add, add namenode IP address to `"/etc/hosts"` 
* Install SSH and copy the SSH keys to the node from other modes in the cluster 
* Copy Hadoop install files to the adding nodes and make proper configuration by synchronizing `"core-site.xml", "mapred-site.xml", "hdfs-site.xml"` from other nodes in cluster only 
* Add the IP address of adding nodes to `"/etc/hosts"` in the namenode 
* Append the IP address of adding nodes to `"conf/slaves"` in the name nodes 
* Start Hadoop thread in adding nodes manually this time (later the name nodes will bring them up if the cluster restart 

        [root@slave-004 Hadoop]# ./bin/hadoop-daemon.sh start datanode
        [root@slave-004 Hadoop]# ./bin/hadoop-daemon.sh start tasktracker

* Balance the data to new added nodes, [root@slave-004 Hadoop]# ./bin/start-balancer.sh

<br>
#### Removing Nodes
* Add list of datanode(s) that needs to decommission to exclude file (assume this file is located under hadoop/conf directory) on namenode.
* Modify the namenode `hdfs-site.xml` config file to set the property for `dfs.hosts.exclude` (see below). ignore this step if you have already have this property set.

        <property>
            <name>dfs.hosts.exclude</name>
            <value>/usr/lib/hadoop/conf/exclude</value>
            <description> List of nodes to decommission </description>
        </property>

* Modify the namenode `mapred-site.xml` config file to set the property for `mapred.hosts.exclude`(see below), ignore this step if you have already have this property set.

        <property>
            <name>mapred.hosts.exclude</name>
            <value>/usr/lib/hadoop/conf/exclude</value>
            <description> List of nodes to decommission </description>
        </property>

* Now update the namenode using following command. 

        [root@slave-004 Hadoop]% hadoop dfsadmin –refreshNodes
* Check the NameNode (50070) admin UI if the state of changed to “Decommission in Progress” for the datanodes that are in exclude list. They will start copying their blocks to other datanodes in the cluster. This process may take minutes to hours depending on data volume on decommissioned nodes
* When all datanodes report their state as “Decommissioned”, then all the blocks have been replicated. Now you can shutdown the all decommissioned nodes (reboot them). 
* Remove decommissioned nodes from slaves file (on all nodes) 
* Now run the following command to refresh the nodes on namenode. [root@slave-004 Hadoop]</span><span style="font-size: 13px;line-height: 19px">% hadoop dfsadmin –refreshNodes 
* Run rebalancer, to make all nodes are ballanced well after adding or removing nodes. 
* Run "hadoop fsck -blocks" command to see how data replication status</span> 

<br>
__Notes:__ You can leave exclude property that you have set previously, but empty the exclude file after decommissioning. 

![](http://i.imgur.com/M11e3Av.png)
