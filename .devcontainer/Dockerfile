FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04

ARG USERNAME=vscode
ENV DEBIAN_FRONTEND noninteractive

ENV DISPLAY :0
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    LC_ALL=C.UTF-8 


RUN apt-get update && apt-get install -y --no-install-recommends build-essential \
    git unzip dosfstools curl wget vim sudo ssh tzdata ca-certificates \
    dbus dbus-x11 alsa-utils pulseaudio pulseaudio-utils mesa-utils x11-apps \
    nodejs npm \
    && apt-get clean -y && rm -rf /var/lib/apt/lists


# node
RUN npm install n -g 
RUN n lts


# maven, gradle
ARG INSTALL_MAVEN="false"
ARG MAVEN_VERSION=""
ARG INSTALL_GRADLE="false"
ARG GRADLE_VERSION=""
RUN if [ "${INSTALL_MAVEN}" = "true" ]; then su vscode -c "umask 0002 && . /usr/local/sdkman/bin/sdkman-init.sh && sdk install maven \"${MAVEN_VERSION}\""; fi \
    && if [ "${INSTALL_GRADLE}" = "true" ]; then su vscode -c "umask 0002 && . /usr/local/sdkman/bin/sdkman-init.sh && sdk install gradle \"${GRADLE_VERSION}\""; fi


USER ${USERNAME}

# JDK, Scala, sbt
SHELL [ "/bin/bash", "-c" ]
RUN curl -s "https://get.sdkman.io" | bash \
    && source "/home/${USERNAME}/.sdkman/bin/sdkman-init.sh" \
    && sdk install java \
    && sdk install scala \
    && sdk install sbt \
    && sbt --version


RUN mkdir -p /home/${USERNAME}/workspace
WORKDIR /home/${USERNAME}/workspace

USER root
COPY set_uid.sh /home/
ENTRYPOINT ["/home/set_uid.sh"]
COPY ./.inject_bashrc /root
RUN cat /root/.inject_bashrc >> /home/${USERNAME}/.bashrc
CMD ["/bin/sh", "-c", "echo Container started ; trap \"exit 0\" 15; while sleep 1 & wait $!; do :; done"]
