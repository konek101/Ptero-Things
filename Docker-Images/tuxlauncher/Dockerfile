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
                && apt install --install-recommends --fix-missing winehq-devel cabextract wine-binfmt -y

# Install winetricks manually
RUN curl -SL https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks -o /usr/local/bin/winetricks \
    && chmod +x /usr/local/bin/winetricks

# Set environment variables
ENV USER=container \
        HOME=/home/container \
        WINEPREFIX=/home/container/.wine \
        WINEDLLOVERRIDES="mscoree,mshtml=" \
        AUTO_UPDATE=1

# Create a non-root user and set permissions
RUN useradd -m -d /home/container -s /bin/bash container && \
    chown -R container:container /home/container

# Switch to non-root user
USER container
WORKDIR /home/container

# Run Wine and Winetricks as the non-root user
RUN WINEARCH=win32 WINEPREFIX=/home/container/.wine wineboot --init && \
    WINEPREFIX=/home/container/.wine winetricks -q dotnet47

STOPSIGNAL SIGINT
COPY --chown=container:container ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Set entrypoint
ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/entrypoint.sh"]
