### Building Ruby on Rails on RHEL 6, RHEL7, SLES 11 and SLES 12

# Installing [Ruby on Rails](http://rubyonrails.org/) on RHEL 6, RHEL7, SLES 11 and SLES 12

The Ruby on Rails code can be installed on a Linux on z System running RHEL 6, RHEL7, SLES 11 and SLES12 by following these instructions:

**NOTE**: When following the steps below please use a standard
permission user unless otherwise specified.

1. First build Ruby 1.9.3+ (the available yum / zypper version of ruby is too low level) from [these instructions](https://github.com/linux-on-ibm-z/docs/wiki/Building-Ruby)

2. Correct the gem environment for a standard user

        export GEM_HOME=/home/<USER>/.gem/ruby
        export PATH=/home/<USER>/.gem/ruby/bin:$PATH
    
 Where `<USER>` is the standard user you are installing under

3. Add a few additional build dependencies
    
 For RHEL6/7,

        yum install -y patch
    
 For SLES11/12,

        zypper install -y patch ruby-devel

4. Install Ruby on Rails via gem

        gem install rails
    
5. Ruby on Rails is now installed (verify with `rails -v`)
