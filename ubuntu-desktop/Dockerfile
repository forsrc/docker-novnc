FROM ubuntu:19.10
MAINTAINER forsrc@gmail.com

ENV DEBIAN_FRONTEND=noninteractive
ENV USER=forsrc
ENV PASSWD=forsrc


RUN apt-get update


RUN apt-get install -y --no-install-recommends ubuntu-desktop && \
          apt-get install -y gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal
          
RUN apt-get install -y nano tightvncserver sudo systemd

RUN apt-get install -y python-numpy websockify novnc

RUN apt-get clean


RUN /bin/dbus-uuidgen --ensure

RUN useradd -m $USER && \
         echo "$USER:$PASSWD" | chpasswd && \
         echo "$USER ALL=(ALL) ALL" >> /etc/sudoers


RUN openssl req -x509 -nodes -newkey rsa:2048 -keyout /home/$USER/novnc.pem -out /home/$USER/novnc.pem -days 365 -subj /C=CN/O="forsrc"/OU="forsrc"/CN="forsrc"/ST="forsrc"/L="forsrc"

RUN mkdir /home/$USER/.vnc
RUN echo '#!/bin/sh'                                                                                               >    /home/$USER/.vnc/xstartup
RUN echo 'xexport XKL_XMODMAP_DISABLE=1'                                        >> /home/$USER/.vnc/xstartup
RUN echo 'xunset SESSION_MANAGER'                                                         >> /home/$USER/.vnc/xstartup
RUN echo 'xunset DBUS_SESSION_BUS_ADDRESS'                                 >> /home/$USER/.vnc/xstartup
RUN echo 'x[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup'            >> /home/$USER/.vnc/xstartup
RUN echo 'x[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources'  >> /home/$USER/.vnc/xstartup
RUN echo 'xxsetroot -solid grey'                                                                        >> /home/$USER/.vnc/xstartup
RUN echo 'xvncconfig -iconic &'                                                                         >> /home/$USER/.vnc/xstartup
RUN echo 'xgnome-panel &'                                                                                >> /home/$USER/.vnc/xstartup
RUN echo 'xgnome-settings-daemon &'                                                        >> /home/$USER/.vnc/xstartup
RUN echo 'xmetacity &'                                                                                         >> /home/$USER/.vnc/xstartup
RUN echo 'xnautilus &'                                                                                          >> /home/$USER/.vnc/xstartup
RUN echo 'xgnome-terminal &'                                                                         >> /home/$USER/.vnc/xstartup

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

EXPOSE 5901
EXPOSE 6080

WORKDIR /home/$USER
USER $USER

CMD ["sh", "-c", "vncserver :1 && websockify --web=/usr/share/novnc/ --cert=/home/forsrc/novnc.pem 6080 localhost:5901"]


