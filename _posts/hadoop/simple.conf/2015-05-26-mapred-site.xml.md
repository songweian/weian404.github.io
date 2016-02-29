---
layout: post
title: mapred-site.xml
category:
tags: hadoop-conf-simple
keywords: 
description: 
--- 
```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
   Licensed to the Apache Software Foundation (ASF) under one or more
   contributor license agreements.  See the NOTICE file distributed with
   this work for additional information regarding copyright ownership.
   The ASF licenses this file to You under the Apache License, Version 2.0
   (the "License"); you may not use this file except in compliance with
   the License.  You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
-->

<!-- Do not modify this file directly.  Instead, copy entries that you -->
<!-- wish to modify from this file into mapred-site.xml and change them -->
<!-- there.  If mapred-site.xml does not already exist, create it.      -->

<configuration>

<!-- i/o properties -->

<property>
  <name>mapreduce.task.io.sort.factor</name>
  <value>100</value>
  <description>The number of streams to merge at once while sorting
  files.  This determines the number of open file handles.</description>
</property>

<property>
  <name>mapreduce.task.io.sort.mb</name>
  <value>512</value>
  <description>The total amount of buffer memory to use while sorting 
  files, in megabytes.  By default, gives each merge stream 1MB, which
  should minimize seeks.</description>
</property>

<property>
  <name>mapreduce.jobtracker.address</name>
  <value>192.168.20.21</value>
  <description>The host and port that the MapReduce job tracker runs
  at.  If "local", then jobs are run in-process as a single map
  and reduce task.
  </description>
</property>

<property>
  <name>mapreduce.jobtracker.http.address</name>
  <value>0.0.0.0:50030</value>
  <description>
    The job tracker http server address and port the server will listen on.
    If the port is 0 then the server will start on a free port.
  </description>
</property>
 

<property>
  <name>mapreduce.job.maps</name>
  <value>2</value>
  <description>The default number of map tasks per job.
  Ignored when mapreduce.jobtracker.address is "local".  
  </description>
</property>

<property>
  <name>mapreduce.job.reduces</name>
  <value>1</value>
  <description>The default number of reduce tasks per job. Typically set to 99%
  of the cluster's reduce capacity, so that if a node fails the reduces can 
  still be executed in a single wave.
  Ignored when mapreduce.jobtracker.address is "local".
  </description>
</property>

<property>
  <name>mapreduce.tasktracker.map.tasks.maximum</name>
  <value>2</value>
  <description>The maximum number of map tasks that will be run
  simultaneously by a task tracker.
  </description>
</property>

<property>
  <name>mapreduce.tasktracker.reduce.tasks.maximum</name>
  <value>2</value>
  <description>The maximum number of reduce tasks that will be run
  simultaneously by a task tracker.
  </description>
</property>

<property>
  <name>mapreduce.map.memory.mb</name>
  <value>1024</value>
  <description>The amount of memory to request from the scheduler for each
  map task.
  </description>
</property>

<property>
  <name>mapreduce.map.cpu.vcores</name>
  <value>1</value>
  <description>The number of virtual cores to request from the scheduler for
  each map task.
  </description>
</property>

<property>
  <name>mapreduce.reduce.memory.mb</name>
  <value>1024</value>
  <description>The amount of memory to request from the scheduler for each
  reduce task.
  </description>
</property>

<property>
  <name>mapreduce.reduce.cpu.vcores</name>
  <value>1</value>
  <description>The number of virtual cores to request from the scheduler for
  each reduce task.
  </description>
</property>

<property>
  <name>mapred.child.java.opts</name>
  <value>-Xmx1024m</value>
  <description>Java opts for the task processes.
  The following symbol, if present, will be interpolated: @taskid@ is replaced 
  by current TaskID. Any other occurrences of '@' will go unchanged.
  For example, to enable verbose gc logging to a file named for the taskid in
  /tmp and to set the heap maximum to be a gigabyte, pass a 'value' of:
        -Xmx1024m -verbose:gc -Xloggc:/tmp/@taskid@.gc
  
  Usage of -Djava.library.path can cause programs to no longer function if
  hadoop native libraries are used. These values should instead be set as part 
  of LD_LIBRARY_PATH in the map / reduce JVM env using the mapreduce.map.env and 
  mapreduce.reduce.env config settings. 
  </description>
</property>

<!-- This is commented out so that it won't override mapred.child.java.opts.-->
<property>
  <name>mapreduce.map.java.opts</name>
  <value>-Xmx1600m</value>
  <description>Java opts only for the child processes that are maps. If set,
  this will be used instead of mapred.child.java.opts.
  </description>
</property>

<!-- This is commented out so that it won't override mapred.child.java.opts.-->
<property>
  <name>mapreduce.reduce.java.opts</name>
  <value>-Xmx1200m</value>
  <description>Java opts only for the child processes that are reduces. If set,
  this will be used instead of mapred.child.java.opts.
  </description>
</property>


<property>
  <name>mapred.child.env</name>
  <value></value>
  <description>User added environment variables for the task processes.
  Example :
  1) A=foo  This will set the env variable A to foo
  2) B=$B:c This is inherit nodemanager's B env variable on Unix.
  3) B=%B%;c This is inherit nodemanager's B env variable on Windows.
  </description>
</property>

<!-- This is commented out so that it won't override mapred.child.env.
<property>
  <name>mapreduce.map.env</name>
  <value></value>
  <description>User added environment variables for the map task processes.
  </description>
</property>
-->

<!-- This is commented out so that it won't override mapred.child.env.
<property>
  <name>mapreduce.reduce.env</name>
  <value></value>
  <description>User added environment variables for the reduce task processes.
  </description>
</property>
-->

<property>
  <name>mapreduce.admin.user.env</name>
  <value></value>
  <description>
  Expert: Additional execution environment entries for 
  map and reduce task processes. This is not an additive property.
  You must preserve the original value if you want your map and
  reduce tasks to have access to native libraries (compression, etc). 
  When this value is empty, the command to set execution 
  envrionment will be OS dependent: 
  For linux, use LD_LIBRARY_PATH=$HADOOP_COMMON_HOME/lib/native.
  For windows, use PATH = %PATH%;%HADOOP_COMMON_HOME%\\bin.
  </description>
</property>

<property>
  <name>mapreduce.map.cpu.vcores</name>
  <value>1</value>
  <description>
      The number of virtual cores required for each map task.
  </description>
</property>

<property>
  <name>mapreduce.reduce.cpu.vcores</name>
  <value>1</value>
  <description>
      The number of virtual cores required for each reduce task.
  </description>
</property>




<!-- Proxy Configuration -->

<!--  Node health script variables -->
 

<!--  end of node health script variables -->

<!-- MR YARN Application properties -->

<property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
  <description>The runtime framework for executing MapReduce jobs.
  Can be one of local, classic or yarn.
  </description>
</property>

<property>
  <name>yarn.app.mapreduce.am.env</name>
  <value></value>
  <description>User added environment variables for the MR App Master 
  processes. Example :
  1) A=foo  This will set the env variable A to foo
  2) B=$B:c This is inherit tasktracker's B env variable.  
  </description>
</property>

<property>
  <name>yarn.app.mapreduce.am.admin.user.env</name>
  <value></value>
  <description> Environment variables for the MR App Master 
  processes for admin purposes. These values are set first and can be 
  overridden by the user env (yarn.app.mapreduce.am.env) Example :
  1) A=foo  This will set the env variable A to foo
  2) B=$B:c This is inherit app master's B env variable.  
  </description>
</property>

<property>
  <name>yarn.app.mapreduce.am.command-opts</name>
  <value>-Xmx1024m</value>
  <description>Java opts for the MR App Master processes.  
  The following symbol, if present, will be interpolated: @taskid@ is replaced 
  by current TaskID. Any other occurrences of '@' will go unchanged.
  For example, to enable verbose gc logging to a file named for the taskid in
  /tmp and to set the heap maximum to be a gigabyte, pass a 'value' of:
        -Xmx1024m -verbose:gc -Xloggc:/tmp/@taskid@.gc
  
  Usage of -Djava.library.path can cause programs to no longer function if
  hadoop native libraries are used. These values should instead be set as part 
  of LD_LIBRARY_PATH in the map / reduce JVM env using the mapreduce.map.env and 
  mapreduce.reduce.env config settings. 
  </description>
</property>

<property>
  <name>yarn.app.mapreduce.am.admin-command-opts</name>
  <value></value>
  <description>Java opts for the MR App Master processes for admin purposes.
  It will appears before the opts set by yarn.app.mapreduce.am.command-opts and
  thus its options can be overridden user. 
  
  Usage of -Djava.library.path can cause programs to no longer function if
  hadoop native libraries are used. These values should instead be set as part 
  of LD_LIBRARY_PATH in the map / reduce JVM env using the mapreduce.map.env and 
  mapreduce.reduce.env config settings. 
  </description>
</property>

<property>
  <name>yarn.app.mapreduce.am.resource.mb</name>
  <value>1536</value>
  <description>The amount of memory the MR AppMaster needs.</description>
</property>

<property>
  <name>yarn.app.mapreduce.am.resource.cpu-vcores</name>
  <value>1</value>
  <description>
      The number of virtual CPU cores the MR AppMaster needs.
  </description>
</property>
 




<!-- jobhistory properties -->


<property>
  <name>yarn.app.mapreduce.am.staging-dir</name>
  <value>/tmp/hadoop-yarn/staging</value>
  <description>The staging dir used while submitting jobs.
  </description>
</property>

</configuration>
```