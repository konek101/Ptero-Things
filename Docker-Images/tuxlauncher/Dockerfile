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
                && curl -sSL -o /etc/apt/sources.list.d/winehq-jammy.sources https://dl.winehq.org/wine-builds/debian/dists/bookworm/winehq-bookworm.sources \
                && apt update -y \
                && apt install -y --no-install-recommends numactl libntlm0 winbind xauth libncurses5:i386 libncurses6:i386 libsdl2-2.0-0:i386 jq libgnutls30 \
                && apt install --install-recommends --fix-missing winehq-devel cabextract wine-binfmt winetricks -y

# Set environment variables
ENV USER=container \
        HOME=/home/container \
        WINEPREFIX=/home/container/.wine \
        WINEDLLOVERRIDES="mscoree,mshtml=" \
        AUTO_UPDATE=1

# Set working directory
WORKDIR /home/container

# Install .NET 4.7 using winetricks
RUN WINEARCH=win32 WINEPREFIX=/home/container/.wine wineboot --init && \
    WINEPREFIX=/home/container/.wine winetricks -q dotnet47

STOPSIGNAL SIGINT
COPY --chown=container:container ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Set entrypoint
ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/entrypoint.sh"]
