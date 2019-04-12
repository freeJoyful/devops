# dev env prepare

## install docker

[follow the steps](https://docs.docker.com/install/linux/docker-ce/centos/)

### change docker root dir

use soft link

```
systemctl stop docker
mv /var/lib/docker /data/docker
ln -s /data/docker /var/lib/docker
systemctl start docker
```

## install docker-compose

[follow the steps](https://docs.docker.com/compose/install/)

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

Test the installation.

```
docker-compose --version
```

## install zsh and autojump

install zsh
```
yum install zsh -y
chsh -s /bin/zsh
yum install git -y
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

install autojump
```
yum install autojump -y
yum install autojump-zsh -y
```

modify `~/.zshrc`
```
plugins=(git autojump)
```

## install python3

```
yum install python-pip -y
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel -y
wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
tar zxvf Python-3.7.2.tgz
cd Python-3.7.2
./configure --enable-shared prefix=/usr/local/python3
make && make install
```

add dynamic lib
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/python3/lib
```

add symbol link
```
ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3.7
ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3.7
```

change python2 to python3.7
```
rm /usr/bin/python
ln -s /usr/bin/python3.7 /usr/bin/python
```

change yum and autojump python
```
vi /usr/bin/yum
change #!/usr/bin/python to #!/usr/bin/python2
vi /usr/libexec/urlgrabber-ext-down
change #!/usr/bin/python to #!/usr/bin/python2
vi /usr/bin/autojump
change #!/usr/bin/python to #!/usr/bin/python2
```

reference:
* https://segmentfault.com/a/1190000015628625

## install vim

```
yum install -y ruby ruby-devel lua lua-devel luajit \
luajit-devel ctags git python python-devel \
python3 python3-devel tcl-devel \
perl perl-devel perl-ExtUtils-ParseXS \
perl-ExtUtils-XSpp perl-ExtUtils-CBuilder \
perl-ExtUtils-Embed
```

```
git clone https://github.com/vim/vim.git
cd vim
./configure --with-features=huge \
            --enable-multibyte \
	    --enable-rubyinterp=yes \
	    --enable-pythoninterp=yes \
	    --with-python-config-dir=/usr/lib64/python2.7/config \
	    --enable-python3interp=yes \
	    --with-python3-config-dir=/usr/local/python3/lib/python3.7/config \
	    --enable-perlinterp=yes \
	    --enable-luainterp=yes \
            --enable-gui=gtk2 \
            --enable-cscope \
	   --prefix=/usr/local

make VIMRUNTIMEDIR=/usr/local/share/vim/vim81
make install
```

change vi lin to vim
```
mv /usr/bin/vi /usr/bin/vi~
ln -s /usr/local/bin/vim /usr/bin/vi
```

reference:
* https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source

## install node

```
curl -sL https://rpm.nodesource.com/setup_11.x | sudo bash -
yum install -y nodejs
```

node and npm version
```
node --version
```

```
output
v11.12.0
```

```
npm --version
```

```
output
6.7.0
```

reference:
* https://linuxize.com/post/how-to-install-node-js-on-centos-7/

## install git

Go to https://git-scm.com/ and check out the latest version of Git
```
wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.21.0.tar.gz
tar zxvf git-2.21.0.tar.gz
```

```
yum groupinstall 'Development Tools'
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-CPAN perl-devel
```

compile
```
cd git-2.21.0
make configure
./configure --prefix=/usr/local
make all
make install
```

## install golang

```
wget https://dl.google.com/go/go1.12.1.linux-amd64.tar.gz
tar -C zxvf go1.12.1.linux-amd64.tar.gz
```

```
export PATH=$PATH:/usr/local/go/bin
```

## install tmux

```
yum -y install libevent-devel
```

```
git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure && make
cp tmux /usr/local/bin
```

install [oh my tmux](https://github.com/gpakosz/.tmux)
```
cd
git clone https://github.com/gpakosz/.tmux.git
ln -s -f .tmux/.tmux.conf
cp .tmux/.tmux.conf.local .
```

reference:
* https://segmentfault.com/a/1190000013012901
* https://github.com/gpakosz/.tmux

## install llvm and clang
first install cmake and ninja

### install [cmake](https://github.com/Kitware/CMake)

```
./bootstrap && make && sudo make install
```

### install [ninja](https://github.com/ninja-build/ninja)

```
git checkout release
./configure.py --bootstrap
cp ninja /usr/local/bin
```

### install clang

sufficient memery is needed(at least 8G maybe), or some error will occur(collect2: fatal error: ld terminated with signal 9)


centos, update gcc to 5.3
```
yum install centos-release-scl -y
yum install devtoolset-7 -y
scl enable devtoolset-7 zsh
gcc --version
```

build clang
```
git clone https://github.com/llvm/llvm-project.git
cd llvm-project
mkdir build
cmake -G Ninja -DLLVM_ENABLE_PROJECTS=clang -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_ASSETION=on ../llvm
cmake --build .
cp -r lib64/python2.7 lib/python.2.7(maybe optional)
cmake --build . -- target install
```

## install  hdfs

#### download a recent stable release

```
cd /etc/
wget http://mirrors.shu.edu.cn/apache/hadoop/common/hadoop-2.9.2/hadoop-2.9.2.tar.gz
```

* unpack
```
tar -xvzf pip-10.0.1.tar.gz
```

* if it occurs the following error
centos gzip:stdin :unexpected end of file 
tar : Unexpected EOF in archive
---- redownload the tar file

#### Configuring Environment of Hadoop Daemons

* To configure Namenode to use parallelGC
```
vim ./etc/hadoop/hadoop-env.sh
export HADOOP_NAMENODE_OPTS="-XX:+UseParallelGC"
```

* specify the HADOOP_PID_DIR and HADOOP_LOG_DIR directories
```
 vim ./etc/hadoop/hadoop-env.sh
 export HADOOP_PID_DIR ="/codeyuguo/packages/hadoop_data_dir/hadoop_pid_dir"
 export HADOOP_LOG_DIR ="/codeyuguo/packages/hadoop_data_dir/hadoop_log_dir"
```

* configure HADOOP_PREFIX in the system-wide shell environment configuration
```
cd /etc/profile.d
vim hadoop.sh
HADOOP_PREFIX=/etc/hadoop-2.9.2/bin
export HADOOP_PREFIX
```

#### Configuring the Hadoop Daemons