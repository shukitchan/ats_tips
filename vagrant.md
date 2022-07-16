(Moved to [ats-alpine](https://github.com/shukitchan/ats-alpine)) Setting up ATS Development Environment quickly on Your Mac
============================================================================================================================

* Install virtualbox
* Install vagrant
* Install git and clone the repository

```
git clone https://github.com/apache/trafficserver.git
```

* Use Vagrantfile in the repository, setup virtual machine and login

```
cd trafficserver
vagrant up xenial_64
vagrant ssh xenial_64
```

* Install necessary packages

```
sudo apt-get update
sudo apt-get install -y autoconf automake build-essential clang libcap-dev libcurl4-openssl-dev libhwloc-dev libpcre3-dev libssl-dev m4 ncurses-dev git luajit libluajit-5.1-dev
```

* Install Support for C++17

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install -y gcc-7
sudo apt-get install -y g++-7
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 20
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 20
```

* Build and Install ATS (Run ldconfig to refresh library module in ubuntu)

```
cd /vagrant
autoreconf -f -i
./configure --enable-experimental-plugins=yes --enable-debug=yes
make 
sudo make install
sudo ldconfig
```
