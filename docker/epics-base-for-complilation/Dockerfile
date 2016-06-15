FROM ubuntu:trusty

# Runtime dependencies
RUN apt-get update && \
    apt-get install -y build-essential libreadline6 libreadline6-dev wget

# Get EPICS source
RUN mkdir /EPICS \
    && cd /EPICS \
    && wget http://www.aps.anl.gov/epics/download/base/baseR3.14.12.5.tar.gz -O EPICS.tar.gz \
    && tar xzf EPICS.tar.gz \
    && mv base-*/ base/ \
    && rm EPICS.tar.gz

# Environment setup
RUN PROFILE_SCRIPT=/etc/profile.d/epics.sh \
    && echo "ETH0_IP=\`ifconfig eth0 | awk '/inet addr/{print substr(\$2,6)}'\`" > $PROFILE_SCRIPT \
    && echo "export EPICS_HOST_ARCH=linux-x86_64" >> $PROFILE_SCRIPT \
    && echo "export EPICS_BASE=/EPICS/base" >> $PROFILE_SCRIPT \
    && echo "export EPICS_CA_ADDR_LIST=localhost" >> $PROFILE_SCRIPT \
    && echo "export EPICS_CA_AUTO_ADDR_LIST=NO" >> $PROFILE_SCRIPT \
    && echo "export EPICS_CAS_INTF_ADDR_LIST=\"localhost \$ETH0_IP\"" >> $PROFILE_SCRIPT \
    && echo "export PATH=/EPICS/base/bin/linux-x86_64:\${PATH}" >> $PROFILE_SCRIPT

# Force all interactive (not just login) shells to load profile
ENV ENV=/etc/profile

# Build and cleanup
RUN cd /EPICS/base \
    && make install \
    && make clean

# Build extensions top level
RUN cd /EPICS \
    && wget http://www.aps.anl.gov/epics/download/extensions/extensionsTop_20120904.tar.gz \
	&& tar -xzf extensionsTop_20120904.tar.gz \
	&& rm extensionsTop_20120904.tar.gz \
	&& cd /EPICS/extensions \
	&& make install

COPY RELEASE.local /EPICS/extensions/src
	
RUN cd /EPICS/extensions/src \
    && wget https://launchpad.net/epics-gateway/trunk/2.0.6.0/+download/gateway2_0_6_0.tar.gz \
    && tar -xzf gateway2_0_6_0.tar.gz \
	&& mv gateway2_0_6_0 gateway \
	&& cd gateway \
	&& make install
	