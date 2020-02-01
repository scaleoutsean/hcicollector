# HCICollector

The HCI Collector is a container-based metrics collection and graphing solution for NetApp HCI and SolidFire systems running Element OS v11.0 or newer.

## Changes

See [CHANGELOG.md](https://github.com/scaleoutsean/hcicollector/blob/master/CHANGELOG.md).

## Description

The HCICollector is a fully packaged metrics collection and graphing solution for Element OS 11+ and vSphere clusters. It is based on these containers:

- sfcollector: container with SolidFire Python SDK. Runs a Python script that collects storage cluster data and feeds it to GraphiteDB
- vmwcollector: vSphere stats collector. Collects vSphere data and feeds it to GraphiteDB
- graphite: database that stores received time series data
- grafana: graphing engine with a Web UI that visualizes Graphite data through easy-to-customize SolidFire and vSphere dashboards

Netapp [Trident](https://netapp-trident.readthedocs.io/) may provide persistent container storage on a NetApp block or file storage system.

### Architecture and Demos

![HCICollector architecture overview](https://github.com/scaleoutsean/hcicollector/blob/master/hcicollector_architecture_overview.jpg)

- [HCICollector walk-through](https://youtu.be/CNXgxkpActo)
- [Volume Histograms demo](https://youtu.be/yggMCgSX2KM) (requires HCICollector v0.7 and Element OS v11.0 or newer)

## Prerequisites

This project has been tested with the following versions of components:

- NetApp HCI (SolidFire, Element OS) v11.3 (v11.0+ should also work)
- VMware vSphere 6.7U3b (please check upstream project documentation)
- NetApp HCI storage and vCenter management accounts with read access to Element storage and vCenter API
- [Optional] Docker CE, Docker Compose and NetApp Trident v19.10 (for external Graphite volume)

Newer or older releases of each component may work. Element OS users with a pre-v11.0 software should check the FAQ document.

## Installation and configuration

- If Docker is not installed, install Docker CE and docker-compose to a Linux OS. Enable and start Docker service.
- Clone hcicollector repository (`git clone https://github.com/scaleoutsean/hcicollector`)
- [Optional] Create a dedicated admin account with reporting-only role. Note that this does not work with install script that uses persistent storage (see the FAQs).
- Edit configuration files or (deprecated, but still works) run the `install_hcicollector.sh` script and provide requested inputs (`cd hcicollector; sudo ./install_hcicollector.sh`)
- Before you start containers create a Docker volume using the volume name chosen in install wizard (`docker volume create --name=graphite`). Note that you wouldn't do this if you chose to use Trident and store data on the array you want to monitor.
- Start the containers (`sudo docker-compose up` (recommended until it works the way you want it) or `sudo docker-compose up -d` (detached mode, for later))

### Update from previous release

- Stop existing containers with docker-compose
- Take a snapshot of the VM and (if using Trident with external storage) external volume
- Delete existing containers
- Backup HCICollector configuration files
- Clone latest release from Github and at first do not run installation script in order to retain configuration files
- Start containers with docker-compose; this should reuse existing configuration files and Graphite volume data

### Use stand-alone SolidFire collector script with existing Grafana and GraphiteDB

Should you want to collect Element storage cluster metrics in your own project or an existing Graphite environment, you may use `solidfire_graphite_collector.py` from this repo (`python3 solidfire_graphite_collector.py -h`). External dependencies iclude [SolidFire Python SDK](https://github.com/solidfire/solidfire-sdk-python).

HCI storage (SolidFire) dashboards may be downloaded from this repo (you'd have to make sure metrics root/space matches your GraphiteDB), or you can create your own dashboards.

## Security

- Currently the configuration files store plain text passwords to SolidFire storage and vSphere.
- It is strongly recommended to create dedicated SolidFire admin account for HCICollector. There's a reporting-only role which can only "view" stuff.
- Ensure that only administrator-level staff has access to HCICollector VM.
- Upstream containers are not audited or regularly checked for vulnerabilities. Feel free to check them on your own.

## Acknowledgments

- This would not have been possible without the prior work of Aaron Patten (@jedimt), cblomart, cbiebers, jmreicha and other contributors
- [solidfire-graphite-collector](https://github.com/cbiebers/solidfire-graphite-collector) - original SolidFire collector script
- [docker-graphite-statsd](https://github.com/graphite-project/docker-graphite-statsd) - Graphite and StatsD container by [Graphite](https://graphiteapp.org/). Documentation can be found [here](https://graphite.readthedocs.io/en/latest/releases.html)
- [vsphere-graphite](https://github.com/cblomart/vsphere-graphite) - vSphere collector for Graphite

## License and Trademarks

- `solidfire_graphite_collector.py`, SolidFire-related dashboards and scripts are licensed under the Apache License, Version 2.0.
- Externally created 3rd party code is licensed under their respective licenses
- NetApp, SolidFire, and the marks listed at www.netapp.com/TM are trademarks of NetApp, Inc. Other marks belong to their respective owners.
