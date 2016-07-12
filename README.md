# r-buildpack-binary

This project contains R binaries built to support upgrade on what was used in
https://github.com/chinling/cf-buildpack-r (forked)

The R binaries are built from source. The original binaries referenced above, i.e.
https://s3.amazonaws.com/r-buildpack/R-3.1.0-binaries-20140629-2201.tar.gz
contains other binaries such as gcc and glibc, both of which remained the same, 
with R replaced by the latest build per above.

Here are instruction on how the binary is built:

## Build [R] binary using Docker container
### Run container in Linux bash shell:
- docker run -it cloudfoundry/cflinuxfs2 bash

### Prepare R (from source) and it's dependencies in the docker container:
- mkdir /prototype
- cd /prototype

- wget https://cran.r-project.org/src/base/R-3/R-3.3.1.tar.gz

- sudo apt-get install gfortran
- sudo apt-get install texlive-latex-base
- sudo apt-get install texlive
- sudo apt-get install texlive-fonts-extra

- wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u92-b14/jdk-8u92-linux-x64.tar.gz
- JAVA_HOME=/prototype/jdk1.8.0_92
- PATH=/prototype/jdk1.8.0_92/bin:$PATH

### Install R:
- cd /prototype
- tar xvfz R-3.3.1.tar.gz
- ./configure --prefix=/home/vcap/R
- make
- make check-all

### Package R with existing gcc and glibc:
- cd /prototype
- wget https://s3.amazonaws.com/r-buildpack/R-3.1.0-binaries-20140629-2201.tar.gz
- mkdir /prototype/prepare
- tar xvfz R-3.1.0-binaries-20140629-2201.tar.gz -C tmp
- cd /prototype/prepare
- rm -rf R
- cp -R /home/vcap/R .
- cd /prototype/prepare
- tar -zcvf R-3.3.1-binaries.tar.gz .

### Move the binary back to your Mac
- docker ps (to get the container ID)
- docker cp <your container ID>:/prototype/prepare/R-3.3.1-binaries.tar.gz /local/proj/sandbox/docker/

