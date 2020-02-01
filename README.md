# HCICollector

The HCI Collector is a container-based metrics collection and graphing solution for NetApp HCI and SolidFire systems running Element OS v11.0 or newer.

## Changes

See [CHANGELOG.md](https://github.com/scaleoutsean/hcicollector/blob/v0.7/CHANGELOG.md).

## Description

The HCICollector is a fully packaged metrics collection and graphing solution for Element OS 11+ and vSphere clusters. It is based on these containers:

- sfcollector: container with SolidFire Python SDK. Runs a Python script that collects storage cluster data and feeds it to GraphiteDB
- vmwcollector: vSphere stats collector. Collects vSphere data and feeds it to GraphiteDB
- graphite: database that stores received time series data
- grafana: graphing engine with a Web UI that visualizes Graphite data through easy-to-customize SolidFire and vSphere dashboards

Advanced users may modify HCICollector to make use of NetApp [Trident](https://netapp-trident.readthedocs.io/) to provide persistent container storage on a NetApp block or file storage system.

### Architecture and Demos

![HCICollector architecture overview](https://github.com/scaleoutsean/hcicollector/blob/v0.7/images/hcicollector_architecture_overview.jpg)

- [HCICollector walk-through](https://youtu.be/CNXgxkpActo)
- [Volume Histograms demo](https://youtu.be/yggMCgSX2KM)

## Prerequisites

This project has been tested with the following:

- Ubuntu 18.04
- Docker CE 19.03.5 and docker-compose 1.24.0 
- NetApp HCI (SolidFire, Element OS) v11.3 (any v11 release will do)
- VMware vSphere 6.7U3b (other 6.x releases should work - please check upstream project documentation)
- NetApp HCI storage and vCenter management accounts with read access to Element storage and vCenter API

Newer or older releases of each component may work. Element OS users with a pre-v11.0 software should check the FAQs.

## Installation and configuration

- Deploy a VM with a sufficiently large disk (say, 50GB)
- Install Docker CE and docker-compose. Enable and restart Docker service
- Clone hcicollector repository (`git clone https://github.com/scaleoutsean/hcicollector`)
- Execute install script and provide requested inputs (`cd hcicollector; sudo ./install_hcicollector.sh`)
- Start the containers (`sudo docker-compose up` (recommended until it works the way you want it; stop with CTRL+C) or `sudo docker-compose up -d` (background mode))
- Access Grafana at http://VM (see Security section about HTTPS) and login with the password you provided to installation wizard (e.g. admin) - you'll be prompted to change it

### Example of installation wizard answers

- SolidFire management virtual IP (MVIP): `192.168.1.30`
- SolidFire username (case sensitive): create a new admin account on your SolidFire cluster (say, `monitor`) and give it a reporting-only role
- Password to use for the Grafana admin account: `admin`
- Internal Docker volume sizse for Graphite in GB: `20`
- IP address of this Docker host: this is the IP of your HCICollector VM

### Update from previous release

- Stop existing containers with docker-compose
- Take a snapshot of the VM and (if using Trident with external storage) external volume
- Delete existing containers
- Backup HCICollector configuration files
- Clone latest release from Github and do not run installation wizard in order to retain configuration files (if you do, mark-out the step which creates local Docker volume)
- Start containers with docker-compose; this should reuse existing configuration files and Graphite volume data

### Use stand-alone SolidFire collector script with existing Grafana and GraphiteDB

Should you want to collect Element storage cluster metrics in your own project or an existing Graphite environment, you may use `solidfire_graphite_collector.py` from this repo (`python3 solidfire_graphite_collector.py -h`). External dependencies include [SolidFire Python SDK](https://github.com/solidfire/solidfire-sdk-python).

HCI storage (SolidFire) dashboards may then by added from this repo (you'd have to edit them to make sure metrics root/space matches your GraphiteDB), or you can create your own dashboards from scratch.

## Security

- Accounts
  - Currently (v11.3 at least) access to Element QoS histograms requires admin-level access to volumes, so if you want better security use a dedicated SolidFire admin account with reporting-only role and do not attempt to visualize histograms. If you want to visualize histograms, you may create a regular admin account for HCICollector (or use existing).
  - It is recommended to create a dedicated vCenter account for vpshere-graphite
  - Grafana by default runs over HTTP. This can be changed (see the FAQs), but at the very least SolidFire or VMware passwords for Grafana
- Configuration files
  - Currently the configuration files store plain text passwords to SolidFire storage and vSphere (see the FAQs)
- HCICollector VM
  - Ensure that only administrator-level staff has access to your HCICollector VM (set a complex password on the VM, run it over HTTPS, etc.)
  - By default, Grafana's Web service runs at port 80 (not 443). See the FAQs on how to use HTTPS. Use a unique account and password for Grafana
- 3rd party containers
  - Upstream containers are not audited or regularly checked for vulnerabilities. Feel free to check them on your own

## Acknowledgments

- This would not have been possible without the prior work of Aaron Patten (@jedimt), cblomart, cbiebers, jmreicha and other contributors
- [solidfire-graphite-collector](https://github.com/cbiebers/solidfire-graphite-collector) - original SolidFire collector script
- [docker-graphite-statsd](https://github.com/graphite-project/docker-graphite-statsd) - Graphite and StatsD container by [Graphite](https://graphiteapp.org/). Documentation can be found [here](https://graphite.readthedocs.io/en/latest/releases.html)
- [vsphere-graphite](https://github.com/cblomart/vsphere-graphite) - vSphere collector for Graphite

## License and Trademarks

- `solidfire_graphite_collector.py`, SolidFire-related dashboards and scripts are licensed under the Apache License, Version 2.0.
- Externally created 3rd party code is licensed under their respective licenses
- NetApp, SolidFire, and the marks listed at www.netapp.com/TM are trademarks of NetApp, Inc. Other marks belong to their respective owners.

