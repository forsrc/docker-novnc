FROM ubuntu:19.10
MAINTAINER forsrc@gmail.com

ENV DEBIAN_FRONTEND=noninteractive
ENV USER=forsrc
ENV PASSWD=forsrc
ENV DISPLAY=1280x1024

RUN apt-get update


RUN apt-get install -y --no-install-recommends ubuntu-desktop && \
          apt-get install -y gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal xterm
          
RUN apt-get install -y nano tightvncserver sudo systemd

RUN apt-get install -y python-numpy websockify novnc

RUN apt-get clean


RUN /bin/dbus-uuidgen --ensure

RUN useradd -m $USER && \
         echo "$USER:$PASSWD" | chpasswd && \
         echo "$USER ALL=(ALL) ALL" >> /etc/sudoers


RUN openssl req -x509 -nodes -newkey rsa:2048 -keyout /home/$USER/novnc.pem -out /home/$USER/novnc.pem -days 365 -subj /C=CN/O="forsrc"/OU="forsrc"/CN="forsrc"/ST="forsrc"/L="forsrc"

RUN mkdir /home/$USER/.vnc

RUN echo '#!/bin/sh'                                                                                             >    /home/$USER/.vnc/xstartup
RUN echo 'export XKL_XMODMAP_DISABLE=1'                                        >> /home/$USER/.vnc/xstartup
RUN echo 'unset SESSION_MANAGER'                                                         >> /home/$USER/.vnc/xstartup
RUN echo 'unset DBUS_SESSION_BUS_ADDRESS'                                 >> /home/$USER/.vnc/xstartup
RUN echo '[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup'            >> /home/$USER/.vnc/xstartup
RUN echo '[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources'  >> /home/$USER/.vnc/xstartup
RUN echo 'xsetroot -solid grey'                                                                        >> /home/$USER/.vnc/xstartup
RUN echo 'vncconfig -iconic &'                                                                         >> /home/$USER/.vnc/xstartup
RUN echo 'gnome-panel &'                                                                                >> /home/$USER/.vnc/xstartup
RUN echo 'gnome-settings-daemon &'                                                        >> /home/$USER/.vnc/xstartup
RUN echo 'metacity &'                                                                                         >> /home/$USER/.vnc/xstartup
RUN echo 'nautilus &'                                                                                          >> /home/$USER/.vnc/xstartup
RUN echo 'gnome-terminal &'                                                                         >> /home/$USER/.vnc/xstartup


RUN echo "${PASSWD}" | vncpasswd -f   >    /home/$USER/.vnc/passwd
RUN echo $PASSWD > password.txt && \
          cat password.txt password.txt | vncpasswd -f && \
          rm password.txt

RUN touch /home/$USER/.Xauthority
RUN chown -R $USER:$USER /home/$USER
RUN chown -R $USER:$USER /home/$USER && \
          chmod 775 /home/$USER/.vnc/xstartup && \
          chmod 600 /home/$USER/.vnc/passwd

#RUN sed -i '0,/port=-1/{s/port=-1/port=5901/}' /etc/xrdp/xrdp.ini

RUN echo "vncserver -kill :1"                                         > /start.sh
RUN echo "vncserver -geometry \$DISPLAY :1"  >> /start.sh
RUN echo "websockify --web=/usr/share/novnc/ --ssl-only --cert=/home/\$USER/novnc.pem --key=/home/\$USER/novnc.pem  6080 localhost:5901" >> /start.sh

RUN chown -R $USER:$USER /start.sh
RUN chmod +x                              /start.sh

EXPOSE 5901
EXPOSE 6080

WORKDIR /home/$USER
USER $USER


CMD ["sh", "-c", "/start.sh"]


