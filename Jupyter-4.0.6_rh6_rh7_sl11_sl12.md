# Building Jupyter Notebook
Jupyter Notebook version 4.0.6 has been successfully built and tested for Linux on z Systems. The following instructions can be used for RHEL 7.1/6.6 and SLES 12/11.

_**General Notes:**_ 	 
_When following the steps below please use a standard permission user unless otherwise specified._


###### 1. Install the following dependencies.

For RHEL6 & RHEL7:

    yum install -y git wget gcc python-devel.s390x gcc-c++
    
For SLES11: 

    zypper install -y git wget gcc gcc-c++ make tar zlib-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel libpcap-devel

SLES11 requires the Python version >= 2.7.* to be installed. 

Download and install Python 3.3.5, setup script for Setuptools and pip:		

	wget http://python.org/ftp/python/3.3.5/Python-3.3.5.tar.xz
	tar xf Python-3.3.5.tar.xz
	cd Python-3.3.5
	./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
	make && make altinstall
	wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
	python3.3 ez_setup.py
	easy_install-3.3 pip

###### 2.Download the source code from GitHub:
	git clone https://github.com/jupyter/notebook.git
	cd /notebook

###### 3.(For SLES11 only)Install the below dependencies required to install notebook:
	easy_install-3.3 pyzmq jinja2 tornado

###### 4.(For RHEL7 only) Download and install pip:
	wget https://bootstrap.pypa.io/get-pip.py
	python get-pip.py 
	
###### 5. Install the Jupyter Notebook using Python’s package manager pip:
	pip install notebook 
        pip install jupyter

