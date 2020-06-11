# Change Log

## Changes in v0.7

- Fork upstream hcicollector by jedimt (this release builds upon upstream branch .v7, here renamed to v0.7)
- Remove NetApp Trident-related steps from install script (see the FAQs). HCICollector now by default uses two local Docker volumes: one for GraphiteDB and another for Grafana settings
- Remove the NetApp Technical Report PDF and video demo files from the repo for faster repository cloning. Add video links to YouTube demo videos
- Changes and improvements to documentation as well as online help (links to SolidFire UI and basic descriptions in various panels)
- Introduce potentially breaking changes in metrics paths and details gathered from SolidFire (see Release Notes v0.7 and FAQs)
- Fixes:
  - SFCollector: wrapper script can contain special characters (issue #2). Set Docker base OS to Alpine v3.1.2
  - SFCollector: SolidFire deduplication efficiency formula changed to reflect space in snapshots (issue #3)
  - Grafana: configure Legend and Axis Y values in most panels to display 0 decimals (enforce integer values where apppropriate (e.g. byte count) and lower the level of unnecesary detail elsewhere)
  - Grafana: change deprecated gauge caunters to new gauge counters
  - Grafana: replace deprecated Grafana renderer with new renderer container
  - Update third party container images (graphite-statsd v1.1.7-2, grafana v6.7.4, vsphere-graphite v0.8b). Grafana v6.7.4 fixes a security risk that does not impact HCICollector because HCICOllector disables the Grafana avatar feature
- Known issues:
  - Built-in dashboard links to SolidFire UI work for configurations with single SolidFire storage cluster. HCICollector environments that monitor multiple SolidFire clusters can add a MVIP variable to dashboard and reference it in URLs to modify URLs on the fly
  - Install script configures only one vCenter cluster and only one SolidFire cluster. See the FAQs for workarounds
  - Some visualizations use Beta-release plugins from Grafana which may have issues. There are bugs in browsers and Grafana
  - Dashboards and panels contain hard-coded URLs (to 192.168.1.30): search-and-replace this link with your own before you import them. HCICollector install script does this for you, but direct import bypasses that step. The proper solution would be to add the MVIP variable to all dashboards and use it in URLs
- Experimental features:
  - Two sample dashboards for hardware monitoring of NetApp H-Series Compute nodes: this is not deployed by default - it requires read-only access to the compute node IPMI interface, manual deployment of collectd VM or container (see the config-examples directory and FAQs) and potentially modifications to the dashboards to make them usable)

## Changes in v0.6.1

- Fix for the bad dedupe factor formula (issue #3) in .v6
- Prior to v0.7, sfcollector used latest version of base OS, so there's a risk to rebuilding containers as base OS updates may break sfcollector
- If you want to try, download branch [v0.6.1](https://github.com/scaleoutsean/hcicollector/tree/v0.6.1) and rebuild, or just apply the [change](https://github.com/jedimt/hcicollector/compare/master...scaleoutsean:v0.6.1) to existing sfcollector/solidfire_graphite_collector.py and rebuild only that container (sfcollector)

## Changes in .v6

- Changed file layout to be more consistent with container names and roles
- Retooled for Grafana 5.0.0
- Dashboards and datasources are now automatically added through the new provisioning functionality in Grafana 5
- Removed the external volume for the Grafana container, only Graphite uses an (optional) external iSCSI volume for persistent data
- Added the ability to poll for active alerts in the "SolidFire Cluster" dashboard.
- Added support for email alerting based on SolidFire events. Note: alerting queries do not support templating variables so if you have multiple clusters you will need to use `*` for the cluster instance instead of the `$Cluster` variable. The net effect of this is that the alert pane will show alerts from ALL clusters instead of an individually selected cluster.
- New detailed install document
- Added a very basic installation script

## Changes in .5

- Extensive dashboard updates. Dashboards now available on [grafana.com](https://grafana.com/dashboards?search=HCI)
- Added additional metrics to collection
- Updated to Trident from NDVP for persistent storage

## Changes in .4

- Added a vSphere collectored based heavily on the work of cblomart's vsphere-graphite collector
- Dashboard updates
- New dashboards for vSphere components

## Changes in .3

- Changed the collector container to Alpine which dramatically cut down container size and build time.
- Other minor changes

## Changes in .v2

- Added "&" in wrapper.sh script to make the collector calls async. Previously the script was waiting for the collector script to finish before continuing the loop. This caused the time between collections to stack which caused holes in the dataset. Now stats should be returned every minute
- Changed graphs to use the summerize function for better accuracy

## Changes in .v1

- Initial release

