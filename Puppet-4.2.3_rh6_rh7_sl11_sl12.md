# Building Puppet
Puppet version 4.3.0 has been successfully built and tested for Linux on z Systems. The following instructions can be used for RHEL 7.1/6.6 and SLES 12/11.

_**General Notes:**_ 	 
_When following the steps below please use a standard permission user unless otherwise specified._

## Puppet Master Installation

###### 1. Install the following dependencies.
#

For RHEL6 & RHEL7:

    yum install -y gcc-c++ readline-devel tar openssl unzip libyaml-devel PackageKit-cron openssl-devel make git wget sqlite-devel glibc-common
    
For SLES11 & SLES12: 

    zypper install -y gcc-c++ readline-devel tar openssl unzip openssl-devel make git wget sqlite-devel glibc-locale

Download and install Ruby:

     wget http://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.2.tar.gz
     tar -xvf ruby-2.2.2.tar.gz
     cd ruby-2.2.2
     ./configure && make && make install
	 cd ..
	 
Download and install RubyGems: 

     wget http://production.cf.rubygems.org/rubygems/rubygems-2.2.2.tgz
     tar -xvf rubygems-2.2.2.tgz
     cd rubygems-2.2.2
     ruby  setup.rb
	 cd ..
    
Install bundler:

     gem install bundler rake-compiler
   
###### 2.Install Puppet. 
#
     gem install puppet -v 4.3.0

###### 3.Locate the $confdir by command.
#
     puppet master --configprint confdir 
The output gives the directory. If such directory does not exist, create one. For example, if the output is /etc/puppetlabs/puppet , then

     mkdir -p /etc/puppetlabs/puppet

###### 4.Create necessary directories and files in  $confdir. 
#
     mkdir /etc/puppetlabs/puppet/modules
     mkdir /etc/puppetlabs/puppet/manifests
     cd /etc/puppetlabs/puppet
	 touch puppet.conf
     wget https://raw.githubusercontent.com/puppetlabs/puppet/master/conf/auth.conf
    
###### 5.Create other necessary directories.
#
     mkdir -p /opt/puppetlabs/puppet
     mkdir -p /var/log/puppetlabs

###### 6.Create "puppet" user and group.
#
     useradd -d /home/puppet -m -s /bin/bash puppet
     puppet resource group puppet ensure=present

###### 7.Add the following parameters to $confdir/puppet.conf (assuming hostname of the master machine is master.myhost.com).
#
     [main]
          logdir = /var/log/puppetlabs
          basemodulepath = /etc/puppetlabs/puppet/modules
          server = master.myhost.com
          user  = puppet
          group = puppet
          pluginsync = false
     [master]
          certname = master.myhost.com
          autosign = true

###### 8.The Puppet master runs on TCP port 8140. This port needs to be open on your master’s firewall (and any intervening firewalls and network devices), and your agent must be able to route and connect to the master. To do this, you need to have an appropriate firewall rule on your master, such as the following rule for the Netfilter firewall.
#
     iptables -A INPUT -p tcp -m state --state NEW --dport 8140 -j ACCEPT 

## Puppet Agent Installation

###### 1. Install the following dependencies. 
#

For RHEL6 & RHEL7:

    yum install -y gcc-c++ readline-devel tar openssl unzip libyaml-devel PackageKit-cron openssl-devel make git wget sqlite-devel glibc-common

For SLES11 & SLES12:

     zypper install -y gcc-c++ readline-devel tar openssl unzip openssl-devel make git wget sqlite-devel glibc-locale

Download and install Ruby: 

     wget http://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.2.tar.gz
     tar -xvf ruby-2.2.2.tar.gz
     cd ruby-2.2.2
     ./configure && make && make install
	 
Download and install RubyGems: 

     wget http://production.cf.rubygems.org/rubygems/rubygems-2.2.2.tgz
     tar -xvf rubygems-2.2.2.tgz
     cd rubygems-2.2.2
     ruby  setup.rb

Install bundler:

     gem install bundler rake-compiler
   
###### 2.Install Puppet. 
#
     gem install puppet -v 4.3.0

###### 3.Locate the  $confdir  by command.
#
     puppet agent --configprint confdir 

The output gives the directory. If such directory does not exist, create one. For example, if the output is /etc/puppetlabs/puppet , then

     mkdir -p /etc/puppetlabs/puppet
    
###### 4.Create necessary directories and files in  $confdir.
#
     cd /etc/puppetlabs/puppet
     mkdir -p /opt/puppetlabs/puppet
     mkdir -p /var/log/puppetlabs
	 touch puppet.conf

###### 5.Add the following parameters to $confdir/puppet.conf (assuming hostname of the master machine is master.myhost.com).
#
     [main]
          logdir = /var/log/puppetlabs
          basemodulepath = /etc/puppetlabs/puppet/modules
          server = master.myhost.com
          user  = puppet
          group = puppet
          pluginsync = false
     [agent]
          certname = master.myhost.com
          report = true
          pluginsync = false
          
###### 6. Add an entry in /etc/hosts file with ipaddress and hostname of master node.
#
      vi /etc/hosts
      <master ipaddress> <master hostname>

## Connecting the Master and Agent for the first time
On the master machine (assuming with hostname master.myhost.com), run the master application:

     puppet master --verbose --no-daemonize 

The --verbose option outputs verbose logging and the --no-daemonize option keeps the daemon in the foreground and redirects output to standard output. You can also add the --debug option to produce more verbose debug output from the daemon.

On the agent application (assuming the hostname of the agent is agent.myhost.com):

     puppet agent --test 

Note: The following errors might be seen after execution of the above step

    Info: Retrieving pluginfacts
    Error: /File[/opt/puppetlabs/puppet/cache/facts.d]: Could not evaluate: Could not retrieve information from environment production source(s) puppet:///pluginfacts
    Info: Retrieving plugin
    Error: /File[/opt/puppetlabs/puppet/cache/lib]: Could not evaluate: Could not retrieve information from environment production source(s) puppet://master.myhost.com/plugins

This is because you don't have any plugins to syn yet, and the pluginsyn property is set to be true by default. So solutions are:

1) Disable the setting in the agent's 'puppet.conf' file by setting  pluginsyn=false. Or 

2) Create at least one plugin

## Testing
For testing, run the tests from the source code.
###### 1.Switch user to puppet, clone Puppet git repository in /home/puppet and execute "bundle install" to install the required gems.

     su puppet
     cd /home/puppet
     git clone --branch 4.3.0 git://github.com/puppetlabs/puppet
     cd puppet
     bundle install --path .bundle/gems/

Edit file_spec.rb to support the testcases in environment.

     cd /home/puppet/puppet
        
Change the below line at number 59 in ```spec/unit/indirector/file_bucket_file/file_spec.rb```

     end.to raise_error(Puppet::FileBucket::BucketError, /Got passed new contents/)

Replace it with

     should compile.and_raise_error(Puppet::FileBucket::BucketError, /Got passed new contents/)
        end

Few testcases needs to be executed as root user and others as puppet user or else the testcases fail.

Execute testcases as root user

* All the unit testcases except ssl, face, indirector, network related testcases must be executed as root user. The other testcases must be executed as puppet user.
* The integration testcases for provider and type should be executed as root user. 

```Note```: Run the  below commands as root user.You can switch to root user by running **exit**, if you are currently switched to puppet user.

Create a shell script. For example ```rootuser_tests.sh``` 

     cd /home/puppet/puppet
     touch rootuser_tests.sh
     chmod 0777 rootuser_tests.sh
        
Add the following content to rootuser_tests.sh.

     #!/bin/bash
     set -e
        
     echo "Running Unit testcases as root user"
     declare -a unittests1
     unittests1=$(ls spec/unit|egrep -v "ssl|face|indirector|network")
     unittest_list1=($unittests1)
     for i in "${unittest_list1[@]}"
     do
      bundle exec rspec "spec/unit/$i"
     done
        
     echo "Running Integration testcases as root user"
     declare -a integration1
     integration1=$(ls spec/integration|egrep "provider|type")
     integration_list1=($integration1)
     for j in "${integration_list1[@]}"
     do
       bundle exec rspec --exclude-pattern ./spec/integration/provider/service/systemd_spec.rb "spec/integration/$j"
     done


Run the shell script

     export LC_ALL="en_US.UTF8"
     ./rootuser_tests.sh
     

Execute testcases as puppet user

* ssl, face, indirector, network related  unit testcases should be executed as puppet user.
* The integration testcases except provider and type related testcases should be executed as puppet user.
* data_binding.rb file is not executed as it does not involve any testcases to be invoked directly.

Create a shell script. For example ```puppetuser_tests.sh```

     cd /home/puppet/puppet
     touch puppetuser_tests.sh
     chmod 0777 puppetuser_tests.sh
        
Add the following content to it.

     #!/bin/bash
     set -e
        
     echo "Running Unit testcases as puppet user"
     declare -a unittests2
     unittests2=$(ls spec/unit|egrep "ssl|face|indirector|network")
     unittest_list2=($unittests2)
     for i in "${unittest_list2[@]}"
      do
        bundle exec rspec "spec/unit/$i"
      done
        
     echo "Running Integration testcases as puppet user"
     declare -a integration2
     integration2=$(ls spec/integration|egrep -v "data_binding.rb|provider|type")
     integration_list2=($integration2)
     for j in "${integration_list2[@]}"
      do
        bundle exec rspec "spec/integration/$j"
     done


Switch user to puppet

     su puppet

Run the shell script

     ./puppetuser_tests.sh
