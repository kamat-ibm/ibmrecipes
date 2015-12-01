
# Base image
FROM sles11

# The author
MAINTAINER LoZ Open Source Ecosystem (https://www.ibm.com/developerworks/community/groups/community/lozopensource)

# Install the following build dependencies:
zypper install -y git \             
                wget \
                gcc \
                gcc-c++ \
				make \
				tar \
				zlib-devel \
				openssl-devel \
				ncurses-devel \
				sqlite-devel \
				readline-devel \
				tk-devel \
				gdbm-devel \
				libpcap-devel

				
# Download and install Python 3.3.5:				
wget http://python.org/ftp/python/3.3.5/Python-3.3.5.tar.xz
tar xf Python-3.3.5.tar.xz
cd Python-3.3.5
./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
make && make altinstall

# Download & install the setup script for Setuptools. Install pip using the newly installed Setuptools:
wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
python3.3 ez_setup.py
easy_install-3.3 pip

# Download source code from GitHub:
git clone https://github.com/jupyter/notebook.git
cd /notebook

# Install the below dependencies required to install notebook:
easy_install-3.3 pyzmq jinja2 tornado

# Install the Jupyter Notebook using Python’s package manager pip:
pip install notebook &&  pip install jupyter

