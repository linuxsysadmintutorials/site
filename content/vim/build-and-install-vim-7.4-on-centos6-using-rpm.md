Title: Build and install vim 7.4 on CentOS 6 using RPM
Date: 2014-09-21   
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/build-and-install-vim-7-4-on-centos-6-using-rpm/
disqus_identifier: 947 http://www.linuxsysadmintutorials.com/?p=947
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Build and install vim 7.4 on CentOS 6 using RPM

## Warning - Post Outdated.
**CentOS 6.7 ships with vim 7.4.629. Instead of following this post,
its recommended that you update to the most current version of CentOS.**

Fed up with vim 7.2 on CentOS 6?

The vim RPM that ships with CentOS7, currently at version 7.4.160, can be rebuilt and installed on a CentOS 6 box.

First, install the source RPM.

```
$ rpm -Uvh http://vault.centos.org/7.0.1406/os/Source/SPackages/vim-7.4.160-1.el7.src.rpm
```

Make a minor change to the vim.spec file with this [patch](https://gist.githubusercontent.com/rene00/897bc360210c2901d6d5/raw/ee34fb3880a8edb38930936aeca2a1ead7f02ef4/vim.spec-centos6-7.4.160-1.patch)

```
$ cd ~/rpmbuild/SPECS
$ wget -O - \
   https://gist.githubusercontent.com/rene00/897bc360210c2901d6d5/raw/ee34fb3880a8edb38930936aeca2a1ead7f02ef4/vim.spec-centos6-7.4.160-1.patch | \
   patch -p0 vim.spec
```

Install the required build dependencies.

```
$ sudo yum install -y ncurses-devel 'perl(ExtUtils::Embed)' libacl-devel \
    autoconf gpm-devel ruby-devel ruby gtk2-devel gtk2-devel libSM-devel \
    libXt-devel libXpm-devel python-devel desktop-file-utils
```

Build the vim-7.4.160 RPM.

```
$ rpmbuild -ba vim.spec
```

The vim-7.4.160 the RPMs ready to be installed will be stored ~/rpmbuilds/RPMS/$(uname -p).

```
$ ls ~/rpmbuild/RPMS/$(uname -p)
vim-common-7.4.160-1.el6.x86_64.rpm      vim-minimal-7.4.160-1.el6.x86_64.rpm
vim-enhanced-7.4.160-1.el6.x86_64.rpm    vim-X11-7.4.160-1.el6.x86_64.rpm
```
