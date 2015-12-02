### Building Ruby on Rails on RHEL 6, RHEL7 and SLES 11

# Installing Ruby on Rails on RHEL 6.6, RHEL7  or SLES 11

The Ruby on Rails code can be installed on a Linux on z System running RHEL 6.6, RHEL7 or SLES 11 by following these instructions:

**NOTE**: When following the steps below please use a standard
permission user unless otherwise specified.

1. First build Ruby 1.9.3+ (the available yum / zypper version of ruby is too low level) from [these instructions](https://github.com/linux-on-ibm-z/docs/wiki/Building-Ruby)

2. Correct the gem environment for a standard user
```
    export GEM_HOME=/home/<USER>/.gem/ruby

    export PATH=/home/<USER>/.gem/ruby/bin:$PATH
```
    Where <USER> is the standard user you are installing under

3. Add a few additional build dependencies
    * patch
    * ruby-devel
    
For RHLE6 & RHEL7,
```
    yum install -y patch
```
For SLES12,
```
    zypper install -y patch ruby-devel
```
3. Add additional dependencies for SLES12 i.e. ruby-devel
```
    zypper install -y patch
```

4. Install Ruby on Rails via gem
```       
    gem install rails
```
5. Set additional environmental variable for SLES12
```
    export PATH=$PATH:/usr/lib64/ruby/gems/2.1.0/gems/railties-4.2.4/bin
```
6. Ruby on Rails is now installed (verify with rails -v)

**Note** :
To run rails application, we need to build runtime Javascript dependency i.e. [Nodejs](https://nodejs.org/en/)
### Building Nodejs on RHEL7 and SLES12
### Section 1: Install the following Dependencies
* git
* java
* gcc-c++

RHEL6 & RHEL7:
```
    yum install --nogpgcheck -y \
        git \
        java-1.8.0-openjdk \
        gcc-c++
```
SLES12:
```
    zypper install -y \
        git \
        java-1_7_0-openjdk \
        gcc-c++
```

### Step 2: Clone the git repository 
```
    git clone https://github.com/andrewlow/node.git
```

### Step 3 : Change work directory to node
```
    cd node/
```
### Step 4 : Build and install nodejs
```
    ./configure
    make
    make install
```
### Step 5 : Create rails new application
```
    rails new test_app
```
### Step 7 : Change directory to test_app
```
    cd test_app/
```
### Step 6 : Run rails server at port 3000
```
    rails s -p 3000
```
**Note** : While running a rails server -p is optional. It will automatically run on port 3000.








