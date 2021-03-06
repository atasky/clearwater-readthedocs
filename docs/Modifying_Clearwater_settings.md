# Modifying Clearwater Settings

This page discusses how to change settings on a Clearwater system. Most settings can be changed on an existing deployment (e.g. security keys and details of external servers), but some are so integral to the system (e.g. the SIP home domain) that the best way to change it is to recreate the Clearwater deployment entirely.

## Modifying Settings in /etc/clearwater/shared_config

*This will have a service impact of up to five minutes.*

Settings in `/etc/clearwater/shared_config` can be safely changed without entirely recreating the system. The one exception to this is the `home_domain`; if you want to change this go to the "Starting from scratch" section instead.

To change one of these settings:

*   On one node of your deployment, run `cw-config download shared_config`. This will download the current version of `shared_config` and specify where it has been downloaded to.
*   Edit the downloaded file to make changes as desired.
*   Run `cw-config upload shared_config` to upload the modified config to etcd. The changes to the shared configuration are logged to `/var/log/syslog` and to the console. Each node in the site picks up the changed shared configuration (using Clearwater's [automatic configuration sharing](Automatic_Clustering_Config_Sharing.md) functionality) and safely restarts itself to use it.
*   You can check which nodes are using the new shared config by running `cw-check_restart_queue_state`. If this command shows that there's been an error (i.e. a node wasn't able to restart after picking up the new config), simply fix the downloaded `shared_config` file and run `cw-config upload shared_config` again.

## Modifying Sprout JSON Configuration

*This configuration can be freely modified without impacting service.*

Some of the more complex sprout-specific configuration is stored in JSON files

* `/etc/clearwater/s-cscf.json` - contains information to allow the Sprout I-CSCF to select an appropriate S-CSCF to handle some requests.
* `/etc/clearwater/bgcf.json` - contains routing rules for the Sprout BGCF.
* `/etc/clearwater/enum.json` - contains ENUM rules when using file-based ENUM instead of an external ENUM server.
* `/etc/clearwater/rph.json` - contains which priority to give to different Resource Priority Header values.

To change one of these files:

*   Run `cw-config download {scscf|bgcf|enum|rph}_json` on *one* node in each site. This will download the current version of `{scscf|bgcf|enum|rph}_json` and will specify where it has been downloaded to.
*   Edit the downloaded file to make changes as desired.
*   Run `cw-config upload {scscf|bgcf|enum|rph}_json` to upload the new config to etcd. The changes to the shared configuration are logged to `/var/log/syslog` and to the console. Each node in the site picks up the changed shared configuration (using Clearwater's [automatic configuration sharing](Automatic_Clustering_Config_Sharing.md) functionality) and will start being used.

## Modifying Sprout XML Configuration

*This configuration can be freely modified without impacting service.*

Some of the more complex sprout-specific configuration is stored in XML files.

* `/etc/clearwater/shared_ifcs.xml` - contains information about all Shared iFC sets for the Sprout S-CSCF to use.
* `/etc/clearwater/fallback_ifcs.xml` - contains information about all the fallback iFCs for the Sprout S-CSCF to apply.

To change one of these files:

*   Run `cw-config download {shared|fallback}_ifcs` on *one* node in each site. This will download the current version of `{shared|fallback}_ifcs` and will specify where it has been downloaded to.
*   Edit the downloaded file to make changes as desired.
*   Run `cw-config upload {shared|fallback}_ifcs` to upload the new config to etcd. The changes to the shared configuration are logged to `/var/log/syslog` and to the console. Each node in the site picks up the changed shared configuration (using Clearwater's [automatic configuration sharing](Automatic_Clustering_Config_Sharing.md) functionality) and will start being used.

## Modifying DNS Config

*This configuration can be freely modified without impacting service*

It's possible to add static overrides to DNS to a clearwater node. This is done by adding entries to `/etc/clearwater/dns.json`. Currently, only CNAME records are supported and this is only used by Chronos and the I-CSCF.

To change this file:

*   Run `cw-config download dns_json` on *one* node in each site. This will download the current version of `dns_json` and will specify where it has been downloaded to.
*   Edit the downloaded file to make changes as desired.
*   Run `cw-config upload dns_json` to upload the new config to etcd. The changes to the shared configuration are logged to `/var/log/syslog` and to the console. Each node in the site picks up the changed shared configuration (using Clearwater's [automatic configuration sharing](Automatic_Clustering_Config_Sharing.md) functionality) and will start being used.

## Starting from scratch

*This will have a service impact of up to half an hour.*

If other settings (such as the Clearwater home domain) are being changed, we recommend that users delete their old deployment and create a new one from scratch, either [with Chef](Creating_a_deployment_with_Chef.md) or [manually](Manual_Install.md). This ensures that the new settings are consistently applied.
