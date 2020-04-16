FROM centos:7
MAINTAINER forsrc@gmail.com

ARG USER=forsrc
ARG PASSWD=forsrc

RUN yum makecache && \
          yum install -y epel*

RUN yum install -y systemd sudo

RUN yum groupinstall -y "X Window system" && \
          yum groupinstall -y xfce

RUN yum --enablerepo=epel* -y install novnc python-websockify numpy

RUN yum install -y tigervnc-server

RUN yum clean all && \
          rm -rf /var/cache/yum/*


RUN /bin/dbus-uuidgen --ensure

RUN useradd -m $USER && \
          echo "$USER:$PASSWD" | chpasswd && \
          echo "$USER ALL=(ALL) ALL" >> /etc/sudoers


RUN openssl req -x509 -nodes -newkey rsa:2048 -keyout /home/$USER/novnc.pem -out /home/$USER/novnc.pem -days 365 -subj /C=CN/O="forsrc"/OU="forsrc"/CN="forsrc"/ST="forsrc"/L="forsrc"

RUN mkdir /home/$USER/.vnc
RUN echo '#!/bin/sh' > /home/$USER/.vnc/xstartup
RUN echo '/usr/bin/startxfce4' >> /home/$USER/.vnc/xstartup
RUN echo "${PASSWD}" | vncpasswd -f > /home/$USER/.vnc/passwd
RUN touch /home/$USER/.Xauthority
RUN chown -R $USER:0 /home/$USER && \
          chmod 775 /home/$USER/.vnc/xstartup && \
          chmod 600 /home/$USER/.vnc/passwd


EXPOSE 5901
EXPOSE 6080

WORKDIR /home/$USER
USER $USER

#ENTRYPOINT ["/usr/bin/vncserver", "-fg"]

CMD ["sh", "-c", "vncserver :1 && websockify --web=/usr/share/novnc/ --cert=/home/forsrc/novnc.pem 6080 localhost:5901"]


