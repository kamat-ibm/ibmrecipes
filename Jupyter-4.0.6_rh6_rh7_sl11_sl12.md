# Building Jupyter Notebook
Jupyter Notebook version 4.0.6 has been successfully built and tested for Linux on z Systems. The following instructions can be used for RHEL 7.1/6.6 and SLES 12/11.

_**General Notes:**_ 	 
_When following the steps below please use a standard permission user unless otherwise specified._


###### 1. Install the following dependencies:

For RHEL7:

    yum install -y git wget gcc python-devel.s390x gcc-c++

For SLES12:

    zypper install -y git wget gcc python-devel.s390x gcc-c++ python-setuptools python-xml

For RHEL6:

    yum install -y wget git tar gcc gcc-c++ xz.s390x zlib-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
    
For SLES11: 

    zypper install -y git wget gcc gcc-c++ make tar zlib-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel libpcap-devel

Jupyter Notebook requires Python version >= 2.7 in order to be installed. 
RHEL6 & SLES11, contains older version of Python. Hence upgarde the Python version as shown below.

Download and install Python 3.3.5 and PIP (For RHEL6 and SLES11):		

	wget http://python.org/ftp/python/3.3.5/Python-3.3.5.tar.xz
	tar xf Python-3.3.5.tar.xz
	cd Python-3.3.5
	./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
	make && make altinstall

###### 2. Install PIP:
    
For RHEL7:

    wget https://bootstrap.pypa.io/get-pip.py
    python get-pip.py

For SLES12:

    easy_install pip
 
For RHEL6/SLES11:

    wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
    python3.3 ez_setup.py
    easy_install-3.3 pip
    easy_install-3.3 pyzmq jinja2 tornado
    
###### 3. Download the source code from GitHub:
	git clone https://github.com/jupyter/notebook.git
	cd /notebook
	
###### 4. Install the Jupyter Notebook using Python’s package manager PIP:
	pip install notebook 
	pip install jupyter
