---
layout: guide
title: StorageOS Docs - Installing on OpenShift 3.9
anchor: platforms
module: platforms/openshift/install-3.9
---

# OpenShift 3.9+

The recommended way to run StorageOS on an OpenShift 3.9+ cluster is to deploy
a daemonset with RBAC support.

## Prerequisites

You will need an OpenShift cluster with Beta APIs enabled.

1. The [iptables rules]({% link _docs/prerequisites/firewalls.md %}) required for StorageOS.
1. Make sure your docker installation has mount propagation enabled.
    ```
   # A successful run is proof of mount propagation enabled
   docker run -it --rm -v /mnt:/mnt:shared busybox sh -c /bin/date

   # In case you see the error, docker: Error response from daemon: linux mounts: Could not find source mount of /mnt
   # you can enable mount propagation by overriding the MountFlag argument
   mkdir -p /etc/systemd/system/docker.service.d/
   cat <<EOF > /etc/systemd/system/docker.service.d/mount_propagation_flags.conf
   [Service]
   MountFlags=shared
   EOF

   systemctl daemon-reload
   systemctl restart docker.service
    ```
1. Enable the `MountPropagation` flag by appending feature gates to the api and controller (you can apply these changes using the Ansible Playbooks)

>Note: If you are using atomic installation rather than origin, the location of the yaml config files and service names might change.

- Add to the KubernetesMasterConfig section (/etc/origin/master/master-config.yaml):

    ```bash
kubernetesMasterConfig:
  apiServerArguments:
      feature-gates:
      - MountPropagation=true
  controllerArguments:
      feature-gates:
      - MountPropagation=true
    ```

- Add to the feature-gates to the kubelet arguments (/etc/origin/node/node-config.yaml):

    ```bash
kubeletArguments:
    feature-gates:
    - MountPropagation=true
    ```

- **Warning:** Restarting OpenShift services can cause downtime in the cluster.
- Restart services in the MasterNode `origin-master-api.service`, `origin-master-controllers.service` and `origin-node.service`
- Restart service in all Nodes `origin-node.service`


## Install StorageOS

```bash
git clone https://github.com/storageos/deploy.git storageos
cd storageos/openshift/deploy-storageos/standard
./deploy-storageos.sh
```