# HCICollector

The HCI Collector is a container-based metrics collection and graphing solution for NetApp HCI and SolidFire systems running Element OS v11.0 or newer.

## Changes

See [CHANGELOG.md](CHANGELOG.md).

## Description

The HCICollector is a fully packaged metrics collection and graphing solution for Element OS 11+ and vSphere clusters. It is based on these containers:

- sfcollector: container with SolidFire Python SDK. Runs a Python script that collects storage cluster data and feeds it to GraphiteDB
- vmwcollector: vSphere stats collector. Collects vSphere data and feeds it to GraphiteDB
- graphite: database that stores received time series data
- grafana: graphing engine with a Web UI that visualizes Graphite data through easy-to-customize SolidFire and vSphere dashboards

Advanced users may modify HCICollector to make use of [NetApp Trident](https://netapp-trident.readthedocs.io/) to provide persistent container storage on a NetApp block or file storage system.

### Architecture and Demos

![HCICollector architecture overview](images/hcicollector_architecture_overview.jpg)

- [HCICollector walk-through](https://youtu.be/CNXgxkpActo)
- [Volume Histograms demo](https://youtu.be/yggMCgSX2KM)

## Prerequisites and requirements

HCICollector has tested with the following configuration:

- Ubuntu 18.04
- Docker CE v19.03.5 and docker-compose v1.24.0 
- NetApp HCI (SolidFire, Element OS) v11.3 (any v11 release will do)
- VMware vSphere 6.7U3b (other 6.x releases should work - please check upstream project documentation)
- NetApp HCI storage and vCenter management accounts with read access to Element storage and vCenter API

Newer or older releases of each component may work. Element OS users with a pre-v11.0 software should check the FAQs.

HCICollector has the following minimum requirements:

- A recent Linux VM with Docker CE v19+
- Read-only access to management interface of SolidFire v11 (Management IP or "MVIP") and vCenter. Note that storage histogram method in Element v11 require Cluster Admin access (this issue should be fixed in v12). HCICollector works with a reporting-only admin account but in that case histogram metrics do not get collected

## Installation and configuration

- Read the Security section below and make a plan based on your security requirements
- Deploy a VM with a sufficiently large disk (say, 50GB)
- Install Docker CE and docker-compose. Enable and restart Docker service
- Clone hcicollector repository (`git clone https://github.com/scaleoutsean/hcicollector`)
- Execute install script and provide requested inputs (`cd hcicollector; sudo ./install_hcicollector.sh`)
- Examine config files and run `sudo docker-compose up` (recommended until it works; stop it with CTRL+C) or `sudo docker-compose up -d` (background mode))
- Access Grafana at the VM port 80 (see Security) and login with the temporary password from installation wizard. Grafana username is `admin`.

### Example of installation wizard answers

- SolidFire management virtual IP (MVIP): `192.168.1.30`
- SolidFire username (case sensitive): `monitor` (we create dedicated SolidFire cluster admin account on SolidFire cluster)
- Password to use for the Grafana admin account: `admin` (temporary password until first login)
- IP address of this Docker host: "public" IP of your HCICollector VM (the VM may have another network to Management LAN)

### Update from previous release

This hasn't been well tested, so your mileage may vary, but for what it's worth:

- Stop existing containers
- Take a snapshot of the VM and (if using Trident with external storage) the external volume used by GraphiteDB
- Delete existing containers with docker-compose. Consider updating Docker binaries
- Backup HCICollector configuration files
- Clone the latest HCICollector release from Github releases section. Do not run installation wizard in order to retain configuration files
- Start containers with docker-compose; this should rebuild the containers, reuse existing configuration files

Or - if your SolidFire environment isn't large - you could setup another instance and transition to it after it's stable.

### Use stand-alone SolidFire collector script with existing Grafana and GraphiteDB

This is not new - it was possible before HCICollector existed - but should be done more often. Maybe don't have vCenter (Hyper-V and KVM users) or if you have existing GraphiteDB.

Should you want to collect Element storage cluster metrics in your own project or an existing Graphite environment, you may use `solidfire_graphite_collector.py` from this repo (`python3 solidfire_graphite_collector.py -h`). External dependencies include [SolidFire Python SDK](https://github.com/solidfire/solidfire-sdk-python).

NetApp HCI storage (SolidFire) dashboards may then by added from this repo (you'd have to edit them to make sure metrics root/space matches your GraphiteDB), or you can create your own dashboards from scratch. Users without VMware vCenter should check out the FAQs.

## Security

- Accounts
  - If you want better security use a dedicated SolidFire admin account with reporting-only role - a downside is histogram metrics won't be collected anyway. If you _do_ want to visualize histograms and have Element v11, you may create a regular admin account for HCICollector (or use existing cluster admin account)
  - It is recommended to create a dedicated vCenter "read-only" account for limited access by vpshere-graphite
  - Grafana in HCICollector by default runs over HTTP. This can be changed (see the FAQs), but at the very least do not use SolidFire or VMware passwords for Grafana authentication
- Configuration files
  - Currently the configuration files store plain text passwords to SolidFire storage and vSphere (see the FAQs)
- HCICollector VM
  - Ensure that only administrator-level staff has access to your HCICollector VM (set a complex password on the VM, access Grafana over HTTPS, etc.)
  - By default, Grafana's Web service runs at port 80 (not 443). See the FAQs on how to configure HTTPS. Use a unique username and password for Grafana
- 3rd party containers
  - Upstream containers are not audited or regularly checked for vulnerabilities. Feel free to check them on your own
- Network
  - If Grafana will be accessed by non-admin users, you should create a multi-homed VM (one public, and one private (Management LAN) interface)

## Acknowledgments

- This would not have been possible without the prior work of Aaron Patten, cblomart, cbiebers, jmreicha and other contributors
- [solidfire-graphite-collector](https://github.com/cbiebers/solidfire-graphite-collector) - original SolidFire collector script
- Main 3rd party applications
  - [docker-graphite-statsd](https://github.com/graphite-project/docker-graphite-statsd) - GraphiteDB and StatsD container by [Graphite](https://graphiteapp.org/). Documentation can be found [here](https://graphite.readthedocs.io/en/latest/releases.html)
  - [vsphere-graphite](https://github.com/cblomart/vsphere-graphite) - VMware vSphere collector for GraphiteDB
  - [Grafana](https://grafana.com)

## License and Trademarks

- `solidfire_graphite_collector.py`, SolidFire-related dashboards and scripts are licensed under the Apache License, Version 2.0
- External, third party containers, scripts and applications may be licensed under their respective licenses
- NetApp, SolidFire, and the marks listed at www.netapp.com/TM are trademarks of NetApp, Inc. Other marks belong to their respective owners

