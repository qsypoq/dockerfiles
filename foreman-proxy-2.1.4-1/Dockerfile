FROM ubuntu:18.04
RUN set -xe  \
	&& echo '#!/bin/sh' > /usr/sbin/policy-rc.d  \
	&& echo 'exit 101' >> /usr/sbin/policy-rc.d  \
	&& chmod +x /usr/sbin/policy-rc.d  \
	&& dpkg-divert --local --rename --add /sbin/initctl  \
	&& cp -a /usr/sbin/policy-rc.d /sbin/initctl  \
	&& sed -i 's/^exit.*/exit 0/' /sbin/initctl  \
	&& echo 'force-unsafe-io' > /etc/dpkg/dpkg.cfg.d/docker-apt-speedup  \
	&& echo 'DPkg::Post-Invoke { "rm -f /var/cache/apt/archives/*.deb /var/cache/apt/archives/partial/*.deb /var/cache/apt/*.bin || true"; };' > /etc/apt/apt.conf.d/docker-clean  \
	&& echo 'APT::Update::Post-Invoke { "rm -f /var/cache/apt/archives/*.deb /var/cache/apt/archives/partial/*.deb /var/cache/apt/*.bin || true"; };' >> /etc/apt/apt.conf.d/docker-clean  \
	&& echo 'Dir::Cache::pkgcache ""; Dir::Cache::srcpkgcache "";' >> /etc/apt/apt.conf.d/docker-clean  \
	&& echo 'Acquire::Languages "none";' > /etc/apt/apt.conf.d/docker-no-languages  \
	&& echo 'Acquire::GzipIndexes "true"; Acquire::CompressionTypes::Order:: "gz";' > /etc/apt/apt.conf.d/docker-gzip-indexes  \
	&& echo 'Apt::AutoRemove::SuggestsImportant "false";' > /etc/apt/apt.conf.d/docker-autoremove-suggests
RUN [ -z "$(apt-get indextargets)" ]
RUN mkdir -p /run/systemd  \
	&& echo 'docker' > /run/systemd/container
CMD ["/bin/bash"]
ARG vcs_ref=42f2862e4ff456b3e8a8db93242d3fcb36586d7a
ARG build_date=2020-10-20T19:48:56
ARG version=2.1.4-2
ARG foreman_proxy_version=2.1.4-1
ARG smart_proxy_remote_execution_version=0.3.0-1

RUN apt-get update  \
	&& echo "deb http://deb.theforeman.org/ bionic 2.1" >> /etc/apt/sources.list.d/foreman.list  \
	&& echo "deb http://deb.theforeman.org/ plugins 2.1" >> /etc/apt/sources.list.d/foreman.list  \
	&& apt-get -y install ca-certificates wget gnupg  \
	&& wget -q https://deb.theforeman.org/pubkey.gpg -O- | apt-key add -  \
	&& apt-get update  \
	&& apt-get -y install foreman-proxy="${foreman_proxy_version}" ruby-smart-proxy-remote-execution-ssh="${smart_proxy_remote_execution_version}"  \
	&& rm -rf /var/lib/apt/lists/*

COPY ./settings.yml /etc/foreman-proxy/settings.yml

ENTRYPOINT ["/usr/share/foreman-proxy/bin/smart-proxy"]
CMD ["--no-daemonize"]

