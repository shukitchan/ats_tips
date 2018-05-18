Setting up ATS Development Environment quickly on Your Mac
==========================================================

* Install virtualbox
* Install vagrant
* Install macports
* Install git through macports and git clone the repostiory

```
sudo port install git
git clone https://github.com/apache/trafficserver.git
```

* Use Vagrantfile in the repository, setup virtual machine and login

```
cd trafficserver
vagrant up trusty_64
vagrant ssh trusty_64
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
