# FAQs

## HELP - shit doesn't wokr and I needed it to work yesterday

If you must have a working solution ASAP, please consider one of alternative solutions (say, NetApp ActiveIQ or the free version of NetApp Cloud Insights) while you figure out how to make this thing work.

This is a tiny community project so you're basically on your own. For "how-to-make-it-work" questions you may try the NetApp HCI channel in NetApp Community Slack (go to https://netapp.io, then join Slack) to see if you can find someone who has experience with HCICollector.

## Where are the configuration files

See the Dockerfile's.

Other files of interest:

* `graphite/carbon.conf` and `graphite/*.conf`: GraphiteDB and StatsD configuration files.
* `solidfire/wrapper.sh`: it is created after configuration is done. It executes `solidfire/solidfire_graphite_collector.py` which gathers metrics and events.

## Why are the passwords stored as plain text

Because they are. Pull requests are welcome. Make sure the config files owned by root and off limits to other users. In fact the entire VM should be. Another thing you should consider is to create a reporting type admin account on NetApp HCI storage or SolidFire (refer to Access Control in the SolidFire API Reference Guide). The same goes for VMware and other monitored components.

If you don't manage the SolidFire or VMware cluster, you may ask the admin(s) to create a reporting-only admin account for you. For VMware vCenter please see the docs from upstream repo.

## How to integrate HCICollector with persistent container storage

Deploy Docker CE or Kubernetes, deploy NetApp Trident (there are two versions - one for Docker and one for Kubernetes - and you probably want the former) storage provisioner and make sure this works with your backend.
Then, instead of creating a local volume for GraphiteDB or any other data you want to persist to external storage, create a volume on external storage (Element OS, ONTAP, etc.) and edit the Docker compose file to make HCICollector store GraphiteDB data on it.

If you want to migrate data from a local to an external volume, you could create a new external volume and copy data from old to new volume. You'd have to do this while GraphiteDB isn't being used, so maybe temporarily modify its Dockerfile and roll back the modification after data has been copied over. Next time the container is started it should be mounting only one (new) Graphite volume on external storage. Crate a snapshot before this if you want to protect your data.

## What do the recommended SolidFire reporting account and Trident have to do with each other

If you use an admin account with all roles (commonly referred to as `admin`), you can create user accounts and volumes. If you use a reporting admin account, you can only watch. Which means that in order to use Trident you'd have to rely on a script to create a new user account for Trident, etc (which requires higher roles) and then modify sfcollector to switch to a reporting account. That's too much trouble and yet another reason why I plan to remove the install script.

## How to update HCICollector from an older version

Users are advised to make a backup or take a snapshot of current version, stop and destroy containers (while leaving configuration files in place), then checkout and start latest release.
If everything works out, any snapshot or backup may be removed if no longer necessary. If you encounter a problem, revert to that backup or snapshot you created.

## Update some component to a newer version

It may work, but it generally isn't recommended unless there's a security bug that affects you, or you want to add functionality unavailable in current release.

## Add own data feeds and dashboards

Feel free to do it by yourself, it's not too difficult. Metrics can be sent to StatsD or directly to Graphite. See `graphite/Dockerfile`. Additional ports may be `EXPOSE`'d if necessary.

## How to add 3rd party feeds and dashboards

Edit Dockerfile to copy them to `grafana/dashboards`. See the answer to "Add own data fees and dashboards" above.

## Feed storage cluster data to existing GraphiteDB

Use the `sfcollector` container. Create a `solidfire/wrapper.sh` to run `solidfire/solidfire_graphite_collector.py` and send it to existing StatsD or Graphite.

* Use `solidfire_graphite_collector.py`. Provide your own Graphite server destination with the `--graphite` argument. You may also need to provide a custom `--metricroot` suitable for your environment.
* Alternatively, modify HCICollector to send data to StatsD first. StatsD can send data to built-in GraphiteDB and also to another Graphite (such as your own). Mind the `metricroot` of secondary destination. You may also modify the script to send data to Telegram or other destnation.

## Alternative approaches to telemetry gathering

Several of the many options:

* Enterprise: please consider either the gratis or paid version of [NetApp Cloud Insights](https://cloud.netapp.com/cloud-insights), a proven, comprehensive, cloud-hosted service for cloud and on-premises environments.
* Enterprise: if you own a NetApp HCI or SolidFire ("storage-only") cluster, you can choose to allow NetApp ActiveIQ to gather metrics and send them to ActiveIQ service, but with better trending and alerting. ActiveIQ also has an API and a mobile application which is superior for support-related monitoring (as opposed to gathering and visualization of performance-related metrics.)
* Gratis: [NABox](https://nabox.org) (at some point it may be able to monitor Element storage clusters; until then you may try to integrate the SolidFire Graphite collector script on your own.)
* Gratis: enable and use SNMP v2/v3 on Element software cluster (as well as other monitored components). This can work with any tool which can receive SNMP traps (Zabbix, [Nagios/Icinga](https://github.com/scaleoutsean/nagfire), etc.)

## Roadmap

At this time the primary goal is to keep components up to date and ensure the thing installs and runs. Depending on time and skills, we may add a thing or two. Tested pull requests are welcome, but maybe create an issue first to discuss it (or just fork this repo and do it any way you want).

## Why was the install script deprecated

Mostly because it was confusing to people unfamiliar with Trident and the aforementioned security concerns. Additionally, current author wants to make it easier to install and use SolidFire collector in existing Grafana setups rather than create new instances of Grafana and Graphite.

## Is this repo associated with or sponspored by NetApp

No, it is not.

