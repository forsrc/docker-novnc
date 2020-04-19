FROM ubuntu:19.10
MAINTAINER forsrc@gmail.com

ENV DEBIAN_FRONTEND=noninteractive
ENV USER=forsrc
ENV PASSWD=forsrc
ENV DISPLAY=1280x1024

RUN apt-get update

#RUN add-apt-repository -y ppa:gnome3-team/gnome3

RUN apt-get install -y  gnome-shell ubuntu-gnome-desktop xterm vino openssh-server
          
RUN apt-get install -y nano tightvncserver sudo systemd

RUN apt-get install -y python-numpy websockify novnc

RUN apt-get clean


#RUN /bin/dbus-uuidgen --ensure

RUN useradd -m $USER && \
         echo "$USER:$PASSWD" | chpasswd && \
         echo "$USER ALL=(ALL) ALL" >> /etc/sudoers

RUN gsettings set org.gnome.Vino require-encryption false

RUN openssl req -x509 -nodes -newkey rsa:2048 -keyout /home/$USER/novnc.pem -out /home/$USER/novnc.pem -days 365 -subj /C=CN/O="forsrc"/OU="forsrc"/CN="forsrc"/ST="forsrc"/L="forsrc"

RUN mkdir /home/$USER/.vnc


RUN echo "${PASSWD}" | vncpasswd -f   >    /home/$USER/.vnc/passwd
RUN echo $PASSWD > password.txt && \
          cat password.txt password.txt | vncpasswd -f && \
          rm password.txt

RUN touch /home/$USER/.Xauthority
RUN chown -R $USER:$USER /home/$USER
RUN chown -R $USER:$USER /home/$USER && \
          chmod 600 /home/$USER/.vnc/passwd

#RUN sed -i '0,/port=-1/{s/port=-1/port=5901/}' /etc/xrdp/xrdp.ini

EXPOSE 5901
EXPOSE 6080

WORKDIR /home/$USER
USER $USER

CMD ["sh", "-c", "vncserver -geometry $DISPLAY :1 && websockify --web=/usr/share/novnc/ --ssl-only --cert=/home/$USER/novnc.pem --key=/home/$USER/novnc.pem  6080 localhost:5901"]
