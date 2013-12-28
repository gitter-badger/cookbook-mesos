Mesos Cookbook
==============
Install Mesos(<http://mesos.apache.org/>) and configure mesos master and slave.  This cookbook also supports installation by both bulding from source and with [Mesosphere](http://mesosophere.io) package.  You can switch installation type by `node[:mesos][:type]` variable (`source` or `mesosphere`).

[BETA] This cookbook also contains some mesos scheduler framework or executors:

* `mesos::docker-exectuor` (only supports Mesos 0.14.0)
* `mesos::marathon-framework` (planned.)

Platform
------------
only support `ubuntu`

Installation Type
----
You have to specify intallation type (`source` or `mesosphere`) by `node[:mesos][:type]` variable.

Recipies
----
### mesos::default
install mesos by `build_from_source` recipe or `mesosphere` recipe.

###mesos::build_from_source
install mesos(download zip from [github](https://github.com/apache/mesos), configure, make, make install).

### mesos::mesosphere
install mesos using mesosphere's mesos package.  You can also install zookeeper package by `node[:mesos][:mesosphere][:with_zookeeper]` if required because Mesospher's mesos package doesn't include zookeeper.

### mesos::master
configure master and cluster deployment configuration files. If you choose `mesosphere`, `node[:mesos][:prefix]` would be overridden by `/usr/local` because mesosphere package installs deploy files to the directory.

* `node[:mesos][:prefix]/var/mesos/deploy/masters`
* `node[:mesos][:prefix]/var/mesos/deploy/slaves`
* `node[:mesos][:prefix]/var/mesos/deploy/mesos-deploy-env.sh`
* `node[:mesos][:prefix]/var/mesos/deploy/mesos-master-env.sh`

if you choose installation type `mesosphere`,  this recipe also confiures upstart conf files which are installed by mesosphere mesos package.

* `/etc/mesos/zk`
* `/etc/defaults/mesos`
* `/etc/defaults/mesos-master`

##### How to configure `mesos-master`
You can configure `mesos-master` command line options by `node[:mesos][:master]` object.  If you have a configuration below,

    node[:mesos][:master] == {
      :port    => "5050",
      :log_dir => "/var/log/mesos",
      :zk      => "zk://localhost:2181/mesos",
      :cluster => "MyCluster"
    }

Then `mesos-master` will be invoked with these options like this.

    mesos-master --zk=zk://localhost:2181/mesos --port=5050 --log_dir=/var/log/mesos --cluster=MyCluster

See the output of `mesos-master --help` for available options.

### mesos::slave
configure slave configuration files.  If you choose `mesosphere`, `node[:mesos][:prefix]` would be overridden by `/usr/local` because mesosphere package installs deploy files to the directory.

* `node[:mesos][:prefix]/var/mesos/deploy/mesos-slave-env.sh`

if you choose installation type `mesosphere`,  this recipe also confiures upstart conf files which are installed by mesosphere mesos package.

* `/etc/mesos/zk`
* `/etc/defaults/mesos`
* `/etc/defaults/mesos-slave`

##### How to configure `mesos-slave`
You can configure `mesos-slave` command line options by `node[:mesos][:slave]` object.  If you have a configuration below,

    node[:mesos][:slave] == {
      :master    => "zk://localhost:2181/mesos",
      :log_dir   => "/var/log/mesos",
      :isolation => "cgroups",
      :work_dir  => "/var/run/work"
    }

Then `mesos-slave` will be invoked with these options like this.

    mesos-slave --master=zk://localhost:2181/mesos --log_dir=/var/log/mesos --isolation=cgroups --work_dir=/var/run/work

See the output of `mesos-slave --help` for available options.

### [BETA] mesos::docker-executor
install [mesos-docker executor](https://github.com/mesosphere/mesos-docker).  currently only Mesos 0.14.0 is supported.

__NOTE__: This cookbook DOES NOT install docker automatically. So, you need to install docker manually. see [./sample/mesosphere/Vagrantfile](https://github.com/everpeace/cookbook-mesos/tree/master/example/mesosphere/Vagrantfile)

Usage
----
please see bellows

* [everpeace/vagrant-mesos](https://github.com/everpeace/vagrant-mesos)
* [./sample/source](https://github.com/everpeace/cookbook-mesos/tree/master/example/source/)
* [./sample/mesosphere](https://github.com/everpeace/cookbook-mesos/tree/master/example/mesosphere/)

Attributes
----------
#### mesos::default
<table>
  <tr>
    <th>Key</th>
    <th>Type</th>
    <th>Description</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><tt>[:mesos][:type]</tt></td>
    <td>String</td>
    <td>installation type(<tt>source</tt> or <tt>mesosphere</tt>)</td>
    <td><tt>source</tt></td>
  </tr>
</table>

#### mesos::build_from_source
<table>
  <tr>
    <th>Key</th>
    <th>Type</th>
    <th>Description</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><tt>[:mesos][:version]</tt></td>
    <td>String</td>
    <td>Version(branch or tag name at http://github.com/apache/mesos).</td>
    <td><tt>0.15.0-rc5</tt></td>
  </tr>
  <tr>
  <td><tt>[:mesos][:prefix]</tt></td>
  <td>String</td>
  <td>Prefix value to be passed to configure script</td>
  <td><tt>/usr/local</tt></td>
  </tr>
  <tr>
  <td><tt>[:mesos][:home]</tt></td>
  <td>String</td>
  <td>Directory which mesos sources are extracted to(<tt>node[:mesos][:home]/mesos</tt>).</td>
  <td><tt>/opt</tt></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:build][:skip_test]</tt></td>
    <td>Boolean</td>
    <td>Flag whether test will be performed.</td>
    <td><tt>true</tt></td>
  </tr>
</table>

#### mesos::mesosphere
<table>
  <tr>
    <th>Key</th>
    <th>Type</th>
    <th>Description</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><tt>[:mesos][:version]</tt></td>
    <td>String</td>
    <td>Version(one of 0.14.0, 0.14.1, 0.14.2, 0.15.0-rc4, 0.15.0-rc5).</td>
    <td><tt>0.15.0-rc5</tt></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:mesosphere][:with_zookeeper]</tt></td>
    <td>String</td>
    <td>flag for installing zookeeper package</td>
    <td><tt>false</tt></td>
  </tr>
</table>

### mesos::master
<table>
  <tr>
    <th>Key</th>
    <th>Type</th>
    <th>Description</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><tt>[:mesos][:prefix]</tt></td>
    <td>String</td>
    <td> Prefix value to be passed to configure script.  This value will be overridden by <tt>/usr/local</tt> when you choose <tt>mesosphere</tt>.</td>
    <td><tt>/usr/local</tt><br/></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:ssh_opt]</tt></td>
    <td>String</td>
    <td>ssh options to be used in <tt>mesos-[start|stop]-cluster</tt></td>
    <td><tt>-o StrictHostKeyChecking=no <br/> -o ConnectTimeout=2</tt></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:deploy_with_sudo]</tt></td>
    <td>String</td>
    <td>Flag whether sudo will be used in <tt>mesos-[start|stop]-cluster</tt></td>
    <td><tt>1</tt></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:cluster_name]</tt></td>
    <td>String</td>
    <td>[OBSOLUTE] Human readable name for the cluster, displayed at webui. </td>
    <td><tt>MyCluster</tt></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:mater_ips]</tt></td>
    <td>Array of String</td>
    <td>IP list of masters used in <tt>mesos-[start|stop]-cluster</tt></td>
    <td>[ ]</td>
  </tr>
  <tr>
    <td><tt>[:mesos][:slave_ips]</tt></td>
    <td>Array of String</td>
    <td>IP list of slaves used in <tt>mesos-[start|stop]-cluster</tt></td>
    <td>[ ]</td>
  </tr>
  <tr>
    <td><tt>[:mesos][:master][:&lt;option_name&gt]</tt></td>
    <td>String</td>
    <td>Command line option for <tt>mesos-master</tt>. see the output of <tt>mesos-master --help</tt> for available options.</td>
    <td></td>
  </tr>
</table>

### mesos::slave
<table>
  <tr>
    <th>Key</th>
    <th>Type</th>
    <th>Description</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><tt>[:mesos][:prefix]</tt></td>
    <td>String</td>
    <td>Prefix value to be passed to configure script.  This value will be overridden by <tt>/usr/local</tt> when you choose <tt>mesosphere</tt>.</td>
    <td><tt>/usr/local</tt></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:slave][:master]</tt></td>
    <td>String</td>
    <td>[REQUIRED] mesos master url.This should be ip:port for non-ZooKeeper based masters, otherwise a zk:// . when <tt>mesosphere</tt>, you should set zk:// address. </td>
    <td></td>
  </tr>
  <tr>
    <td><tt>[:mesos][:slave][:&lt;option_name&gt]</tt></td>
    <td>String</td>
    <td>Command line option for <tt>mesos-slave</tt>. see the output of <tt>mesos-slave --help</tt> for available options.</td>
    <td></td>
  </tr>
</table>


Contributing
------------

1. Fork the repository on Github
2. Create a named feature branch (like `add_component_x`)
3. Write you change
4. Write tests for your change (if applicable)
5. Run the tests, ensuring they all pass
6. Submit a Pull Request using Github

License and Authors
-------------------
* Author:: Shingo Omura everpeace@gmail.com

Copyright:: 2009-2013 Shingo Omura, All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
