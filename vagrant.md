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

* Build and Install ATS (Run ldconfig to refresh library module in ubuntu)

```
cd /vagrant
autoreconf -f -i
./configure --enable-experimental-plugins=yes --enable-debug=yes
make 
sudo make install
sudo ldconfig
```
