# <a name="title"></a> chef-logstash [![Build Status](https://secure.travis-ci.org/lusis/chef-logstash.png?branch=master)](http://travis-ci.org/lusis/chef-logstash)

Description
===========
This is the semi-official 'all-in-one' Logstash cookbook.


Requirements
============
All of the requirements are explicitly defined in the recipes. Every effort has been made to utilize official Opscode cookbooks.

However if you wish to use an external ElasticSearch cluster, you will need to install that yourself and change the relevant attributes for discovery.
The same applies to integration with Graphite.

This cookbook has been tested together with the following cookbooks

* [Heavywater Graphite Cookbook](https://github.com/heavywater/chef-graphite) - This is the one I use
* [Karmi's ElasticSearch Cookbook](https://github.com/karmi/cookbook-elasticsearch)


Attributes
==========

## Default

* `node['logstash']['basedir']` - the base directory for all the Logstash components
* `node['logstash']['user']` - the owner for all Logstash components
* `node['logstash']['group']` - the group for all Logstash components
* `node['logstash']['graphite_role']` - the Chef role to search for discovering your preexisting Graphite server
* `node['logstash']['elasticsearch_role']` - the Chef role to search for discovering your preexisting ElasticSearch cluster.
* `node['logstash']['elasticsearch_cluster']` - the cluster name assigned to your preexisting ElasticSearch cluster. Only applies to external ES clusters.
* `node['logstash']['elasticsearch_ip']` - the IP address that will be used for your elasticsearch server in case you are using Chef-solo
* `node['logstash']['graphite_ip']` - the IP address that will be used for your graphite server in case you are using Chef-solo
* `node['logstash']['join_groups']` - An array of Operative System groups to join. Usefull to gain read privileges on some logfiles.


## Agent

* `node['logstash']['agent']['install_method']` - The method to install logstash - either `jar` or `source`, defaults to `jar`
* `node['logstash']['agent']['version']` - The version of Logstash to install. Only applies to `jar` install method.
* `node['logstash']['agent']['source_url']` - The URL of the Logstash jar to download. Only applies to `jar` install method.
* `node['logstash']['agent']['checksum']` - The checksum of the jar file. Only applies to `jar` install method.
* `node['logstash']['agent']['base_config']` - The name of the template to use for `logstash.conf` as a base config.
* `node['logstash']['agent']['base_config_cookbook']` - Where to find the base\_config template.
* `node['logstash']['agent']['xms']` - The minimum memory to assign the JVM.
* `node['logstash']['agent']['xmx']` - The maximum memory to assign the JVM.
* `node['logstash']['agent']['java_opts']` - Additional params you want to pass to the JVM
* `node['logstash']['agent']['gc_opts']` - Specify your garbage collection options to pass to the JVM
* `node['logstash']['agent']['ipv4_only']` - Add jvm option preferIPv4Stack?
* `node['logstash']['agent']['debug']` - Run logstash with `-v` option?
* `node['logstash']['agent']['server_role']` - The role of the node behaving as a Logstash `server`/`indexer`
* `node['logstash']['agent']['inputs']` - Array of input plugins configuration.
* `node['logstash']['agent']['filters']` - Array of filter plugins configuration.
* `node['logstash']['agent']['outputs']` - Array of output plugins configuration.

## Server

* `node['logstash']['server']['install_method']` - The method to install logstash - either `jar` or `source`
* `node['logstash']['server']['version']` - The version of Logstash to install. Only applies to `jar` install method.
* `node['logstash']['server']['source_url']` - The URL of the Logstash jar to download. Only applies to `jar` install method.
* `node['logstash']['server']['checksum']` - The checksum of the jar file. Only applies to `jar` install method.
* `node['logstash']['server']['base_config']` - The name of the template to use for `logstash.conf` as a base config.
* `node['logstash']['server']['base_config_cookbook']` - Where to find the base\_config template.
* `node['logstash']['server']['xms']` - The minimum memory to assign the JVM.
* `node['logstash']['server']['xmx']` - The maximum memory to assign the JVM.
* `node['logstash']['server']['java_opts']` - Additional params you want to pass to the JVM
* `node['logstash']['server']['gc_opts']` - Specify your garbage collection options to pass to the JVM
* `node['logstash']['server']['ipv4_only']` - Add jvm option preferIPv4Stack?
* `node['logstash']['server']['debug']` - Run logstash with `-v` option?
* `node['logstash']['server']['enable_embedded_es']` - Should Logstash run with the embedded ElasticSearch server or not?
* `node['logstash']['server']['install_rabbitmq']` - Should this recipe install rabbitmq?
* `node['logstash']['server']['inputs']` - Array of input plugins configuration.
* `node['logstash']['server']['filters']` - Array of filter plugins configuration.
* `node['logstash']['server']['outputs']` - Array of output plugins configuration.

## Kibana

* `node['logstash']['kibana']['repo']` - The git repo to install Kibana from.
* `node['logstash']['kibana']['sha']` - The sha/branch of the repo you wish to clone.
* `node['logstash']['kibana']['apache_template']` - The name of the template file to use for the Apache site file
* `node['logstash']['kibana']['config']` - The name of the template to use for the Kibana `config.php` file
* `node['logstash']['kibana']['server_name']` - The value to use for the Apache `ServerName` variable to use for the Kibana Apache virtual host.
* `node['logstash']['kibana']['http_port']` - The port the virtualhost kibana listens on

## Source

* `node['logstash']['source']['repo']` - The git repo to use for the source code of Logstash
* `node['logstash']['source']['sha']` - The sha/branch of the repo you wish to clone.
* `node['logstash']['source']['java_home']` - your `JAVA_HOME` location. Needed explicity for `ant` when building JRuby

## Index Cleaner

* `node['logstash']['index_cleaner']['days_to_keep']` - Integer number of days from today of Logstash index to keep.

Usage
=====
A proper readme is forthcoming but in the interim....

There are 3 recipes you need to concern yourself with:

* server - This would be your indexer node
* agent - This would be a local host's agent for collection
* kibana - This is the web interface

Every attempt (and I mean this) was made to ensure that the following objectives were met:

* Any agent install can talk to a server install
* Kibana web interface can talk to the server install
* Each component works OOB and with each other
* Utilize official opscode cookbooks where possible

This setup makes HEAVY use of roles. Additionally, ALL paths have been made into attributes. Everything I could think of that would need to be customized has been made an attribute.

## Defaults
By default, the recipes look for the following roles (defined as attributes so they can be overridden):

* `graphite_server` - `node['logstash']['graphite_role']`
* `elasticsearch_server` - `node['logstash']['elasticsearch_role']`
* `logstash_server` - `node['logstash']['kibana']['elasticsearch_role']` and `node['logstash']['agent']['server_role']`

The reason for giving `kibana` its own role assignment is to allow you to point to existing ES clusters/logstash installs.

The reason for giving `agent` its own role assignment is to allow the `server` and `agent` recipes to work together.

Yes, if you have a graphite installation with a role of `graphite_server`, logstash will send stats of events received to `logstash.events`.

## Agent and Server configuration
The template to use for configuration is made an attribute as well. This allows you to define your OWN logstash configuration file without mucking with the default templates.

The `server` will, by default, enable the embedded ES server. This can be overriden as well.

See the `server` and `agent` attributes for more details.

## Source vs. Jar install methods
Both `agent` and `server` support an attribute for how to install. By default this is set to `jar` to use the 1.1.1preview as it is required to use elasticsearch 0.19.4. The current release is defined in attributes if you choose to go the `source` route.

## Out of the box behaviour
Here are some basic steps

* Create a role called `logstash_server` and assign it the following recipes: `logstash::server` and `logstash::kibana`
* Assign the role to a new server
* Assign the `logstash::agent` recipe to another server

If there is a system found with the `logstash_server` role, the agent will automatically configure itself to send logs to it over tcp port 5959. This is, not coincidently, the port used by the chef logstash handler.

If there is NOT a system with the `logstash_server` role, the agent will use a null output. The default input is to read files from `/var/log/*.log` excluding and gzipped files.

If you point your browser to the `logstash_server` system's ip address, you should get the kibana web interface.

Do something to generate a new line in any of the files in the agent's watch path (I like to SSH to the host), and the events will start showing up in kibana. You might have to issue a fresh empty search.

The `pyshipper` recipe will work as well but it is NOT wired up to anything yet.

## Letting data drive your templates

The current templates for the agent and server are written so that you can provide ruby hashes in your roles that map to inputs, filters, and outputs. Here is a role for logstash_server

    name "logstash_server"
    description "Attributes and run_lists specific to FAO's logstash instance"
    default_attributes(
      :logstash => {
        :server => {
          :enable_embedded_es => false,
          :inputs => [
            :amqp => {
              :type => "all",
              :host => "127.0.0.1",
              :exchange => "rawlogs",
              :name => "rawlogs_consumer"
            }
          ],
          :filters => [
            :grok => {
              :type => "haproxy",
              :pattern => "%{HAPROXYHTTP}",
              :patterns_dir => '/opt/logstash/server/etc/patterns/'
            }
          ],
          :outputs => [
            :file => {
              :type => 'haproxy',
              :path => '/opt/logstash/server/haproxy_logs/%{request_header_host}.log',
              :message_format => '%{client_ip} - - [%{accept_date}] "%{http_request}" %{http_status_code} ....'
            }
          ]
        }
      }
    )
    run_list(
      "role[elasticsearch_server]",
      "recipe[logstash::server]",
      "recipe[php::module_curl]",
      "recipe[logstash::kibana]"
    )


It will produce the following logstash.conf file

    input {

      amqp {
        exchange => 'rawlogs'
        host => '127.0.0.1'
        name => 'rawlogs_consumer'
        type => 'all'
      }
    }

    filter {

      grok {
        pattern => '%{HAPROXYHTTP}'
        patterns_dir => '/opt/logstash/server/etc/patterns/'
        type => 'haproxy'
      }
    }

    output {
      stdout { debug => true debug_format => "json" }
      elasticsearch { host => "127.0.0.1" cluster => "logstash" }

      file {
        message_format => '%{client_ip} - - [%{accept_date}] "%{http_request}" %{http_status_code} ....'
        path => '/opt/logstash/server/haproxy_logs/%{request_header_host}.log'
        type => 'haproxy'
      }
    }


Here is an example using multiple filters

    default_attributes(
                   :logstash => {
                     :server => {
                       :filters => [
                                    { :grep => {
                                        :type => 'tomcat',
                                        :match => { '@message' => '([Ee]xception|Failure:|Error:)' },
                                        :add_tag => 'exception',
                                        :drop => false
                                    } },
                                    { :grep => {
                                        :type => 'tomcat',
                                        :match => { '@message' => 'Unloading class ' },
                                        :add_tag => 'unloading-class',
                                        :drop => false
                                    } },
                                    { :multiline => {
                                        :type => 'tomcat',
                                        :pattern => '^\s',
                                        :what => 'previous'
                                    } }
                                   ]
                      }
                    }
                  }
    )

It will produce the following logstash.conf file

    filter {

      grep {
        add_tag => 'exception'
        drop => false
        match => ['@message', '([Ee]xception|Failure:|Error:)']
        type => 'tomcat'
      }

      grep {
        add_tag => 'unloading-class'
        drop => false
        match => ["@message", "Unloading class "]
        type => 'tomcat'
      }

      multiline {
        patterns_dir => '/opt/logstash/patterns'
        pattern => '^\s'
        type => 'tomcat'
        what => 'previous'
      }

    }



# BIG WARNING

* Currently only tested on Ubuntu Natty, Precise, and RHEL 6.2.


## License and Author

- Author::                John E. Vincent
- Author::                Bryan W. Berry (<bryan.berry@gmail.com>)
- Author::                Richard Clamp (@richardc)
- Author::                Juanje Ojeda (@juanje)
- Author::                @benattar
- Copyright::             2012, John E. Vincent
- Copyright::             2012, Bryan W. Berry
- Copyright::             2012, Richard Clamp
- Copyright::             2012, Juanje Ojeda
- Copyright::             2012, @benattar


Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
