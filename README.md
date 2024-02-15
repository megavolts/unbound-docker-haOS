# Unbound DNS Server Docker Image

Run Unbound with latest version of OpenSSL on Home Assistant OS/Raspberry Pi with Docker.

Forked from mvance/unbound-docker-rpi

## Supported tags and respective `Dockerfile` links

- [`1.17.0`, `latest` (*1.17.0/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.17.0)
- [`1.16.3`, (*1.16.3/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.16.3)
- [`1.16.2`, (*1.16.2/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.16.2)
- [`1.16.1`, (*1.16.1/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.16.1)
- [`1.16.0`, (*1.16.0/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.16.0)
- [`1.15.0`, (*1.15.0/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.15.0)
- [`1.14.0`, (*1.14.0/Dockerfile*)](https://github.com/MatthewVance/unbound-docker-rpi/tree/master/1.14.0)

## What is Unbound?

Unbound is a validating, recursive, and caching DNS resolver.
> [unbound.net](https://unbound.net/)

## How to use this image

This fork of mvance/unbound-docker-rpi is configured as a recursive server, according to the example provided by mvance/unbound. It does not forward quiers to Cloudflare DNS server over TLS.


### Serve Custom DNS Records for Local Network (not tested)

While Unbound is not a full authoritative name server, it supports resolving
custom entries on a small, private LAN. In other words, you can use Unbound to
resolve fake names such as your-computer.local within your LAN.

To support such custom entries using this image, you need to provide an
`a-records.conf` file. This conf file is where you will define your custom
entries for forward and reverse resolution.

The `a-records.conf` file should use the following format:

```
# A Record
  #local-data: "somecomputer.local. A 192.168.1.1"
  local-data: "laptop.local. A 192.168.1.2"

# PTR Record
  #local-data-ptr: "192.168.1.1 somecomputer.local."
  local-data-ptr: "192.168.1.2 laptop.local."
```

Once the file has your entries in it, mount your version of the file as a volume
when starting the container:

```console
docker run \
--name=unbound-rpi \
--volume=$(pwd)/a-records.conf:/opt/unbound/etc/unbound/a-records.conf:ro \
--publish=53:53/udp \
--publish=53:53/tcp \
--restart=unless-stopped \
--detach=true \
mvance/unbound-rpi:latest
```

#### SRV records (not tested)

The `srv-records.conf` file should use the following format:

```
# SRV records
# _service._proto.name. | TTL | class | SRV | priority | weight | port | target.
_etcd-server-ssl._tcp.domain.local.  86400 IN    SRV 0        10     2380 etcd-0.domain.local.
_etcd-server-ssl._tcp.domain.local.  86400 IN    SRV 0        10     2380 etcd-1.domain.local.
_etcd-server-ssl._tcp.domain.local.  86400 IN    SRV 0        10     2380 etcd-2.domain.local.
```

## Acknowledgments

The code in this image is heavily influenced by DNSCrypt server Docker image,
though the upstream projects most certainly also deserve credit for making this
all possible.
- [Docker](https://www.docker.com/)
- [DNSCrypt server Docker image](https://github.com/jedisct1/dnscrypt-server-docker)
- [OpenSSL](https://www.openssl.org/)
- [Unbound](https://unbound.nlnetlabs.nl/)

## Licenses

### License

Unless otherwise specified, all code is released under the MIT License (MIT).
See the [repository's `LICENSE`
file](https://github.com/MatthewVance/unbound-docker-rpi/blob/master/LICENSE) for
details.

### Licenses for other components

- Docker: [Apache 2.0](https://github.com/docker/docker/blob/master/LICENSE)
- DNSCrypt server Docker image: [ISC License](https://github.com/jedisct1/dnscrypt-server-docker/blob/master/LICENSE)
- OpenSSL: [Apache-style license](https://www.openssl.org/source/license.html)
- Unbound: [BSD License](https://unbound.nlnetlabs.nl/svn/trunk/LICENSE)
