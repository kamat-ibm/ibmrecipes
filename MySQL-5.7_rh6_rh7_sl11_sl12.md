MySQL can be built for Linux on z Systems running RHEL 6/7.1 and SLES 11/12 by following these instructions. Version 5.7.10 has been successfully built & tested this way.
More information on MySQL is available at https://www.mysql.com and the source code can be downloaded from https://github.com/mysql/mysql-server.git
.

_**General Notes:**_

i) _**Note:** When following the steps below please use a standard permission user unless otherwise specified._

ii) _**Note:** A directory `/<source_root>/` will be referred to in these instructions, this is a temporary writeable directory anywhere you'd like to place it._

## Building MySQL

###Obtain pre-built dependencies and create `/<source_root>/` directory.

   1. Use the following commands to obtain dependencies :

    For RHEL 6
    ```shell
    sudo yum install git gcc gcc-c++ make cmake bison ncurses-devel
    ```
    For RHEL 7.1
    ```shell
    sudo yum install git gcc gcc-c++ make cmake bison ncurses-devel perl-Data-Dumper
    ```
    For SLES 11 - (Additional support packages are needed to update cmake)
    ```shell
    sudo zypper install git gcc gcc-c++ make cmake bison ncurses-devel util-linux tar zip wget
    ```
    For SLES 12
    ```shell
    sudo zypper install git gcc gcc-c++ make cmake bison ncurses-devel
    ```

   1. Create the `/<source_root>/` directory mentioned above.

    ```shell
    mkdir /<source_root>/
    ```
    
###Dependency Build

   _**Only Required on SLES 11**  - Update cmake to version 3.4.1 and GCC to version 6.0.0 by building from source._

   1. _[Optional]_ Check the version of any existing `cmake` & `GCC` executable.
    ```shell
      which cmake
      $(which cmake) --version
    ```
      _**Note:** A `cmake` at version 2.6.3 or later and `GCC` version 4.4 or later should be usable without upgrade.Later version of             `GCC` can be built from [these instructions](https://github.com/linux-on-ibm-z/docs/wiki/Building-gccgo)._
     

   1. Download the cmake source code, then extract it.
      ```shell
      cd /<source_root>/
      sudo wget --no-check-certificate https://cmake.org/files/v3.4/cmake-3.4.1.tar.gz
      sudo tar xzf cmake-3.4.1.tar.gz
      ```

   1. Bootstrap to configure the Makefile. Then Make and Install the utility.
      ```shell
      cd cmake-3.4.1
      sudo ./bootstrap --prefix=/usr
      sudo gmake
      sudo gmake install
      ```
      _**Note:** To place `cmake` in the standard SLES location use `./bootstrap --prefix=/usr`._


   1. Confirm the location and version of the upgraded `cmake`.
      ```shell
      which cmake
      $(which cmake) --version
      ```
    
   1. Confirm the version of GCC.
      ```shell
      gcc --version
      ```
   
   Note: GCC might still point to the older version.Run the commands below to link it to the latest version.
      ```shell
      update-alternatives --install /usr/bin/gcc gcc /opt/gccgo/bin/gcc 50
      update-alternatives --install /usr/bin/g++ g++ /opt/gccgo/bin/g++ 50
      update-alternatives --install /usr/bin/cpp cpp /opt/gccgo/bin/cpp 50
      update-alternatives --install /usr/bin/c++ c++ /opt/gccgo/bin/c++ 50
      update-alternatives --install /usr/bin/cc cc /opt/gccgo/bin/gcc 50
      export LD_LIBRARY_PATH=/opt/gccgo/lib64
      ```
   
###Product Build - MySQL.

   1. Download the MySQL 5.7.10 source code from Github.
    ```shell
    cd /<source_root>/
    git clone https://github.com/mysql/mysql-server.git
    ```

   1. Move into the ` mysql-server` sub-directory, and checkout branch 5.7
    ```shell
    cd mysql-server
    git branch
    git checkout 5.7
    ```
    _**Note:** At the time of writing branch 5.7 returned minor version 5.7.10, - this minor version is subject to change._


   1. Configure and Build the MySQL Software.
    ```shell
    sudo cmake . -DDOWNLOAD_BOOST=1 -DWITH_BOOST=.
    sudo gmake
    ```

   1. _[Optional]_ Check the make

    The testing should take only a few seconds. All 21 tests should PASS.
    ```shell
    gmake test
    ```

   1. Install the Software into the standard location.
    ```shell
    sudo gmake install
    ```

###_[Optional]_ Post installation Setup and Testing.

   This guideline demonstrates a basic free-standing MySQL database, with a script for shutdown/restart as a System Service.
   Refer to http://www.mysql.com for definitive details, and configuration for non-test environments.

   1. Add a MySQL userid and group, then reset Linux ownerships and permissions within `/usr/local/mysql`.
    ```shell
    sudo /usr/sbin/groupadd mysql
    sudo /usr/sbin/useradd -g mysql mysql
    sudo chown -R mysql.mysql /usr/local/mysql
    ```

   1. Initialize MySQL Data Directory.  (The `--user=mysql`, to match the MySQL Daemon (mysqld) userid).
    ```shell
    cd /usr/local/mysql
    bin/mysqld --initialize --user=mysql
    ```

   1. _[Optional]_ Start/Stop the mysqld daemon.
    ```shell
    cd /<source_root>/
    sudo /usr/local/mysql/bin/mysqld_safe --user=mysql &
    /usr/local/mysql/bin/mysqladmin version
    sudo /usr/local/mysql/bin/mysqladmin -u root shutdown
    ```
     _**Note:** Performing a version check while the daemon is running confirms MySQL is operational._

   1. To start and stop server as an init.d Service

    This can be manually tested with a Start/Stop, but a system restart is needed for a full test.
    ```shell
    cd /usr/local/mysql
    sudo  cp support-files/mysql.server /etc/init.d/mysql
    sudo /etc/init.d/mysql start
    /usr/local/mysql/bin/mysqlshow
    sudo /etc/init.d/mysql stop
    ```
    _**Note:** i). Operation of bin/mysql commands requires, a running mysql server, and the `mysqlshow` executable is to show the existing databases._

    _**Note:** ii). See http://www.mysql.com for full details,  ... where a Linux root password is set, the bin/mysqladmin and bin/mysql commands require -u and -p options.
            For example: `bin/mysqladmin -u root -p      version`. The system will prompt `Enter password:`  expecting the root password in response._

###_[Optional]_ Clean up.

   1. Remove the ` /<source_root>/` directory to tidy up.

     ```shell
     cd /<source_root>/
     cd ..
     sudo rm -rf /<source_root>/
     ```

###References:

https://bugs.mysql.com/bug.php?id=72752 - Explanation of the cmake upgrade for SLES 11.

http://www.mysql.com - MySQL Homepage with definitive Information and Documentation.
