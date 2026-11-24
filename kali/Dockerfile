FROM kalilinux/kali-rolling

# starter, or an official metapackage such as kali-linux-headless
ARG TOOLSET=starter

COPY packages.starter /tmp/packages.starter

RUN set -eux; \
    export DEBIAN_FRONTEND=noninteractive; \
    apt-get update; \
    apt-get install -y --no-install-recommends ca-certificates locales; \
    case "$TOOLSET" in \
      starter) \
        grep -vE '^[[:space:]]*#|^[[:space:]]*$' /tmp/packages.starter \
          | xargs apt-get install -y --no-install-recommends; \
        ;; \
      kali-*) \
        apt-get install -y "$TOOLSET"; \
        ;; \
      *) \
        echo "Unknown TOOLSET: $TOOLSET" >&2; \
        echo "Use starter or a Kali metapackage name such as kali-linux-headless." >&2; \
        exit 1; \
        ;; \
    esac; \
    sed -i 's/^# *\(en_US.UTF-8 UTF-8\)/\1/' /etc/locale.gen; \
    locale-gen; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/* /tmp/packages.starter

ENV LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8

RUN set -eux; \
    printf '%s\n' \
      'hack-stack' \
      'Practice only on the local lab (http://juice-shop:3000) or hosts you are authorized to test.' \
      'Host folder ./work is mounted at /work.' \
      'Wordlists: /usr/share/wordlists  (rockyou is gzipped until you decompress it)' \
      > /etc/motd; \
    touch /root/.bashrc; \
    printf '%s\n' '[[ $- == *i* ]] && cat /etc/motd' | cat - /root/.bashrc > /tmp/bashrc; \
    mv /tmp/bashrc /root/.bashrc

WORKDIR /work

# Stay running so the container can be started and stopped without an open terminal.
CMD ["sleep", "infinity"]
