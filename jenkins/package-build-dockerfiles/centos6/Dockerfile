FROM centos:6
MAINTAINER Brett Smith <brett@curoverse.com>

# Install build dependencies provided in base distribution
RUN yum -q -y install make automake gcc gcc-c++ libyaml-devel patch readline-devel zlib-devel libffi-devel openssl-devel bzip2 libtool bison sqlite-devel rpm-build git perl-ExtUtils-MakeMaker fuse-devel libattr-devel nss-devel libcurl-devel which tar scl-utils centos-release-SCL postgresql-devel

# Install golang binary
ADD generated/golang-amd64.tar.gz /usr/local/
RUN ln -s /usr/local/go/bin/go /usr/local/bin/

# Install RVM
RUN gpg --keyserver pool.sks-keyservers.net --recv-keys D39DC0E3 && \
    curl -L https://get.rvm.io | bash -s stable && \
    /usr/local/rvm/bin/rvm install 2.1 && \
    /usr/local/rvm/bin/rvm alias create default ruby-2.1 && \
    /usr/local/rvm/bin/rvm-exec default gem install bundle fpm

# Need to "touch" RPM database to workaround bug in interaction between
# overlayfs and yum (https://bugzilla.redhat.com/show_bug.cgi?id=1213602)
RUN touch /var/lib/rpm/* && yum -q -y install python27 python33
RUN scl enable python33 "easy_install-3.3 pip" && scl enable python27 "easy_install-2.7 pip"

RUN cd /tmp && \
    curl -OL 'http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm' && \
    rpm -ivh rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm && \
    sed -i 's/enabled = 0/enabled = 1/' /etc/yum.repos.d/rpmforge.repo

RUN touch /var/lib/rpm/* && yum install --assumeyes git

ENV WORKSPACE /arvados
CMD ["scl", "enable", "python33", "python27", "/usr/local/rvm/bin/rvm-exec default bash /jenkins/run-build-packages.sh --target centos6"]
