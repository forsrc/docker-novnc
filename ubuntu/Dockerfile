FROM ubuntu:19.10
MAINTAINER forsrc@gmail.com

ENV DEBIAN_FRONTEND=noninteractive
ARG USER
ENV USER=${USER:-forsrc}
ARG PASSWD
ARG PASSWD=${PASSWD:-forsrc}
ENV DISPLAY=1280x1024


RUN apt-get update

RUN apt-get install -y nano xorg lxde-core lxterminal xrdp firefox tightvncserver sudo

RUN apt-get install -y python-numpy websockify novnc

RUN apt-get clean


RUN /bin/dbus-uuidgen --ensure

RUN useradd -m --shell /bin/bash $USER && \
    echo "$USER:$PASSWD" | chpasswd && \
    echo "$USER ALL=(ALL) ALL" >> /etc/sudoers


RUN openssl req -x509 -nodes -newkey rsa:2048 -keyout /home/$USER/novnc.pem -out /home/$USER/novnc.pem -days 365 -subj /C=CN/O="forsrc"/OU="forsrc"/CN="forsrc"/ST="forsrc"/L="forsrc"

RUN mkdir /home/$USER/.vnc
RUN echo '#!/bin/sh'                       >  /home/$USER/.vnc/xstartup
RUN echo 'xrdb $HOME/.Xresources'          >> /home/$USER/.vnc/xstartup
RUN echo 'xsetroot -solid grey'            >> /home/$USER/.vnc/xstartup
RUN echo 'export XKL_XMODMAP_DISABLE=1'    >> /home/$USER/.vnc/xstartup
RUN echo '/etc/X11/Xsession'               >> /home/$USER/.vnc/xstartup
RUN echo 'lxterminal &'                    >> /home/$USER/.vnc/xstartup
RUN echo '/usr/bin/lxsession -s LXDE &'    >> /home/$USER/.vnc/xstartup
RUN echo "${PASSWD}" | vncpasswd -f        >  /home/$USER/.vnc/passwd
RUN echo $PASSWD > password.txt && \
    cat password.txt password.txt | vncpasswd -f && \
    rm password.txt

RUN touch /home/$USER/.Xauthority
RUN chown -R $USER:$USER /home/$USER
RUN chown -R $USER:$USER /home/$USER && \
    chmod 775 /home/$USER/.vnc/xstartup && \
    chmod 600 /home/$USER/.vnc/passwd

RUN sed -i '0,/port=-1/{s/port=-1/port=5901/}' /etc/xrdp/xrdp.ini

RUN echo "rm -rf /tmp/.X1-lock /tmp/.X11-unix" >  /start.sh
RUN echo "vncserver -kill :1"                  >> /start.sh
RUN echo "vncserver -geometry \$DISPLAY :1"    >> /start.sh
RUN echo "websockify --web=/usr/share/novnc/ --ssl-only --cert=/home/\$USER/novnc.pem --key=/home/\$USER/novnc.pem  6080 localhost:5901" >> /start.sh

RUN chown -R $USER:$USER /start.sh
RUN chmod +x             /start.sh

EXPOSE 5901
EXPOSE 6080

WORKDIR /home/$USER
USER $USER

#CMD ["sh", "-c", "vncserver -geometry $DISPLAY :1 && websockify --web=/usr/share/novnc/ --ssl-only --cert=/home/$USER/novnc.pem --key=/home/$USER/novnc.pem  6080 localhost:5901"]

CMD ["sh", "-c", "/start.sh"]
