# ------------------------------------------------ #
# Docker Image for AstroTuxLauncher on Pterodactyl #
# ------------------------------------------------ #
FROM    ghcr.io/parkervcp/yolks:debian
LABEL   author="Kringle" maintainer="imkringle@proton.me"
# Cred to https://github.com/Dalethium for helping cleanup
ENV     DEBIAN_FRONTEND=noninteractive

# Install required packages / Wine
RUN     dpkg --add-architecture i386 \
                && apt update -y \
                && cd /tmp/ \
                && mkdir -pm755 /etc/apt/keyrings \
                && curl -sSL -o /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key \
                && curl -sSL -o /etc/apt/sources.list.d/winehq-bookworm.sources https://dl.winehq.org/wine-builds/debian/dists/bookworm/winehq-bookworm.sources \
                && apt update -y \
                && apt install -y --no-install-recommends numactl libntlm0 winbind xauth libncurses5:i386 libncurses6:i386 libsdl2-2.0-0:i386 jq libgnutls30 \
                && apt install --install-recommends --fix-missing winehq-stable cabextract wine-binfmt -y\
                && apt install -y --no-install-recommends libasound2-plugins:i386 \
                libx11-6:i386 libxext6:i386 libfreetype6:i386 libglu1-mesa:i386 \
                libxcursor1:i386 libxrandr2:i386 libxi6:i386 libxinerama1:i386 \
                libxcomposite1:i386 libxrender1:i386 libxxf86vm1:i386
            



# Set environment variables
ENV USER=container \
        HOME=/home/container \
        WINEPREFIX=/home/container/.wine \
        WINEDLLOVERRIDES="mscoree,mshtml=" \
        AUTO_UPDATE=1 \
        XDG_RUNTIME_DIR=/tmp/runtime \
        DISPLAY=:99

# Install winetricks manually
RUN curl -SL https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks -o /usr/local/bin/winetricks \
    && chmod +x /usr/local/bin/winetricks



RUN apt-get update && apt-get install -y \
    xvfb \
    && rm -rf /var/lib/apt/lists/*

    
  


# Ensure the existing user has correct ownership
RUN chown -R container:container /home/container

# Run Wine and Winetricks as the non-root user
USER container

RUN echo '#!/bin/bash\n\
    nohup Xvfb :99 -screen 0 1280x1024x24 &\n\
    sleep 5\n\
    WINEARCH=win64 WINEPREFIX=/home/container/.wine wineboot --init\n\
    WINEPREFIX=/home/container/.wine winetricks --force -q dotnet8' > /home/container/setup.sh \
    && chmod +x /home/container/setup.sh
#RUN rm -rf /home/container/.wine && \
#    WINEARCH=win32 WINEPREFIX=/home/container/.wine wineboot --init && \
#    WINEPREFIX=/home/container/.wine winetricks --force -q dotnet8

RUN /home/container/setup.sh

USER root

STOPSIGNAL SIGINT
COPY ./entrypoint.sh /usr/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/winetricks
RUN chmod +x /usr/bin/entrypoint.sh

# Set entrypoint
ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/usr/bin/entrypoint.sh"]
