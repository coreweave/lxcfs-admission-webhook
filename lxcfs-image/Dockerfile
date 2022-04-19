FROM ubuntu:20.04 as build
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update -y
RUN apt-get install -y fuse libfuse-dev wget build-essential meson python3 python3-jinja2 cmake pkg-config

ENV LXCFS_VERSION 5.0.0
RUN wget https://linuxcontainers.org/downloads/lxcfs/lxcfs-$LXCFS_VERSION.tar.gz && \
	mkdir /lxcfs && tar xzvf lxcfs-$LXCFS_VERSION.tar.gz -C /lxcfs  --strip-components=1 && \
	cd /lxcfs && meson setup -Dinit-script=systemd --prefix=/lxcfs build/ && \
    meson install -C build/

FROM ubuntu:20.04
STOPSIGNAL SIGINT
COPY --from=build /lxcfs/bin/lxcfs /usr/local/bin/lxcfs
COPY --from=build /lxcfs/lib/x86_64-linux-gnu/lxcfs/liblxcfs.so /usr/lib/x86_64-linux-gnu/liblxcfs.so
COPY --from=build /usr/lib/x86_64-linux-gnu/libfuse.so.2.9.9 /usr/lib/x86_64-linux-gnu/libfuse.so.2.9.9
COPY --from=build /usr/lib/x86_64-linux-gnu/libulockmgr.so.1.0.1 /usr/lib/x86_64-linux-gnu/libulockmgr.so.1.0.1

RUN ldconfig

RUN mkdir -p /usr/local/lib/lxcfs /var/lib/lxcfs

COPY start.sh /
CMD ["/start.sh"]
