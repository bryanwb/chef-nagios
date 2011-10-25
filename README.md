Description
===========

Installs and configures Nagios 3 for a server and for clients using Chef search capabilities.

Changes
=======

## v1.0.0:

* Use Chef 0.10's `node.chef_environment` instead of `node['app_environment']`.
* source installation support on both client and server sides
* initial RHEL/CentOS/Fedora support

Requirements
============

Chef
----

Chef version 0.10.0+ is required for chef environment usage. See __Environments__ under __Usage__ below.

A data bag named 'users' should exist, see __Data Bag__ below.

The monitoring server that uses this recipe should have a role named 'monitoring' or similar, this is settable via an attribute. See __Attributes__ below.

Because of the heavy use of search, this recipe will not work with Chef Solo, as it cannot do any searches without a server.

Platform
--------

* RHEL, CentOS, Fedora

Tested on RHEL 5.5 and 6.1

Cookbooks
---------

* build-essential
* xinetd

Attributes
==========

default
-------

The following attributes are used by both client and server recipes.

* `node['nagios']['user']` - nagios user, default 'nagios'.
* `node['nagios']['group']` - nagios group, default 'nagios'.
* `node['nagios']['plugin_dir']` - location where nagios plugins go,
* default '/usr/lib/nagios/plugins'.

client_xinetd
-------------
This recipe uses the xinet daemon to run the nagios client instead of the nagios nrpe daemon. This provides for better logging opportunities as the nrpe daemon is otherwise difficult to log. 

This recipe depends on the xinetd cookbook

The client_xinetd uses the following attributes

* `node['nagios']['client']['install_method']` - whether to install from package or source. Default chosen by platform based on known packages available for Nagios 3: debian/ubuntu 'package', redhat/centos/fedora/scientific: source
* `node['nagios']['plugins']['url']` - url to retrieve the plugins source
* `node['nagios']['plugins']['version']` - version of the plugins
* `node['nagios']['plugins']['checksum']` - checksum of the plugins source tarball
* `node['nagios']['nrpe']['home']` - home directory of nrpe, default /usr/lib/nagios
* `node['nagios']['nrpe']['conf_dir']` - location of the nrpe configuration, default /etc/nagios
* `node['nagios']['nrpe']['url']` - url to retrieve nrpe source
* `node['nagios']['nrpe']['version']` - version of nrpe to download
* `node['nagios']['nrpe']['checksum']` - checksum of the nrpe source tarball
*  node['nagios_server'] - the ip address of the nagios server in case there isn't a server with nagios_server in it run list, as some setups may not use chef to configure their server


Libraries
=========

default
-------

The library included with the cookbook provides some helper methods used in templates.

* nagios_boolean
* nagios_interval - calculates interval based on interval length and a given number of seconds.
* nagios_attr - retrieves a nagios attribute from the node.

Usage
=====

See below under __Environments__ for how to set up Chef 0.10 environment for use with this cookbook.

For a Nagios server, create a role named 'monitoring', and add the following recipe to the run_list:

    recipe[nagios::server]

This will allow client nodes to search for the server by this role and add its IP address to the allowed list for NRPE.

To install Nagios and NRPE on a client node:

    include_recipe "nagios::client"

This is a fairly complicated cookbook. For a walkthrough and example usage please see [Opscode's Nagios Quick Start](http://help.opscode.com/kb/otherhelp/nagios-quick-start).

Environments
------------

The searches used are confined to the node's `chef_environment`. If you do not use any environments (Chef 0.10+ feature) the `_default` environment is used, which is applied to all nodes in the Chef Server that are not in another defined role. To use environments, create them as files in your chef-repo, then upload them to the Chef Server.

    % cat environments/production.rb
    name "production"
    description "Systems in the Production Environment"

    % knife environment from file production.rb

License and Author
==================

Author:: Joshua Sierles <joshua@37signals.com>
Author:: Nathan Haneysmith <nathan@opscode.com>
Author:: Joshua Timberman <joshua@opscode.com>
Author:: Seth Chisamore <schisamo@opscode.com>
Author:: Bryan W. Berry <bryan.berry@gmail.com>

Copyright 2009, 37signals
Copyright 2009-2011, Opscode, Inc
copyright 2011, Bryan W. Berry

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
