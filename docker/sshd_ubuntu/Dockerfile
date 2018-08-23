FROM ubuntu:16.04
MAINTAINER fly 1002557401@qq.com
RUN apt-get update&&apt-get install -y openssh-server
RUN mkdir -p /root/.ssh
RUN mkdir -p /var/run/sshd   #
RUN sed -ri 's/PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/ UsePAM no/g' /etc/ssh/sshd_config
ADD authorized_keys /root/.ssh/authorized_keys
EXPOSE 22
CMD ["/usr/sbin/sshd","-D"]
