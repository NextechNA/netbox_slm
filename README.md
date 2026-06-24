# NetBox SLM

<p align="center"><i>NetBox SLM is a plugin for lifecycle management of software components, including versions and installations.</i></p>

<div align="center">
<a href="https://github.com/NextechNA/netbox_slm/releases"><img src="https://img.shields.io/github/v/release/NextechNA/netbox_slm" alt="Release"/></a>
<a href="https://img.shields.io/badge/NetBox-4.5.x--4.6.x-2b9348"><img src="https://img.shields.io/badge/NetBox-4.5.x--4.6.x-2b9348" alt="NetBox Compatibility"/></a>
<a href="https://github.com/NextechNA/netbox_slm/stargazers"><img src="https://img.shields.io/github/stars/NextechNA/netbox_slm" alt="Stars Badge"/></a>
<a href="https://github.com/NextechNA/netbox_slm/network/members"><img src="https://img.shields.io/github/forks/NextechNA/netbox_slm" alt="Forks Badge"/></a>
<a href="https://github.com/NextechNA/netbox_slm/pulls"><img src="https://img.shields.io/github/issues-pr/NextechNA/netbox_slm" alt="Pull Requests Badge"/></a>
<a href="https://github.com/NextechNA/netbox_slm/issues"><img src="https://img.shields.io/github/issues/NextechNA/netbox_slm" alt="Issues Badge"/></a>
<a href="https://github.com/NextechNA/netbox_slm/graphs/contributors"><img alt="GitHub contributors" src="https://img.shields.io/github/contributors/NextechNA/netbox_slm?color=2b9348"></a>
<a href="https://github.com/NextechNA/netbox_slm/blob/master/LICENSE"><img src="https://img.shields.io/github/license/NextechNA/netbox_slm?color=2b9348" alt="License Badge"/></a>
</div>

> **Maintained fork.** This is a compatibility-maintained fork of
> [ICTU/netbox_slm](https://github.com/ICTU/netbox_slm) (Apache-2.0), published by
> NextechNA to keep the plugin working with current NetBox and Django releases while
> upstream development is inactive. Original authorship and license are retained.

## Compatibility

| NetBox | Django | netbox-slm (this fork) |
| ------ | ------ | ---------------------- |
| 4.5.x  | 5.2    | >= 1.8.5               |
| 4.6.x  | 6.0    | >= 1.8.5               |

Requires Python >= 3.11. Because the fork is not published to PyPI, installations
reference this repository directly (see below).

## Installation Guide

### Standard (virtual-environment) install

1. Install the plugin into the NetBox virtual environment:

```bash
source /opt/netbox/venv/bin/activate
pip install "netbox-slm @ git+https://github.com/NextechNA/netbox_slm.git@v1.8.5"
```

2. Pin it in ``local_requirements.txt`` so it survives NetBox upgrades. Add this line to
   the ``local_requirements.txt`` in the version directory you upgrade **into**:
   netbox-slm @ git+https://github.com/NextechNA/netbox_slm.git@v1.8.5
3. Enable the plugin in ``/opt/netbox/netbox/netbox/configuration.py``:

```python
PLUGINS = [
    "netbox_slm",
]
```

4. Apply migrations and restart NetBox:

```bash
python /opt/netbox/netbox/manage.py migrate
sudo systemctl restart netbox netbox-rq
```

> This release makes **no database schema changes** and introduces no new migrations;
> the migration step is a no-op on existing installations and existing data is unaffected.

### Docker install

When using the Docker version of NetBox, first follow the `netbox-docker` [quickstart](https://github.com/netbox-community/netbox-docker#quickstart) instructions to clone the `netbox-docker` repository and set up the ``docker-compose.override.yml``.

Next, follow these instructions (based on the netbox-docker [plugin instructions](https://github.com/netbox-community/netbox-docker/wiki/Using-Netbox-Plugins)) to install the NetBox SLM plugin:

1. Add ``netbox_slm`` to the ``PLUGINS`` list in
   ``configuration/plugins.py``.
2. Create a ``plugin_requirements.txt`` with the following contents:
netbox-slm @ git+https://github.com/NextechNA/netbox_slm.git@v1.8.5
3. Create a ``Dockerfile-SLM`` with contents:

```dockerfile
FROM netboxcommunity/netbox:vX.Y.Z

COPY ./plugin_requirements.txt /opt/netbox/
RUN /usr/local/bin/uv pip install -r /opt/netbox/plugin_requirements.txt
```

   Replace ``vX.Y.Z`` with a NetBox image tag in the 4.5.x–4.6.x range.

4. Create a ``docker-compose.override.yml`` with contents:

```yaml
services:
  netbox:
    ports:
      - "8000:8080"
    build:
      context: .
      dockerfile: Dockerfile-SLM
    image: netbox:slm
  netbox-worker:
    image: netbox:slm
  netbox-housekeeping:
    image: netbox:slm
```

Now, build the image: ``docker compose build --no-cache``

And finally, run NetBox with the SLM plugin: ``docker compose up``

## Get in touch

This fork is maintained by NextechNA. Please report issues via this repository's
[issue tracker](https://github.com/NextechNA/netbox_slm/issues/new). It is a fork of the
original [ICTU/netbox_slm](https://github.com/ICTU/netbox_slm) by
[Mart Visser](https://github.com/MartVisser) and contributors.
