---
layout: guide
title: StorageOS Docs - Production deployments
anchor: concepts
module: concepts/production
---

# Production deployments

## External key-value store

StorageOS uses a key-value store to store cluster configuration. For ease of
use, etcd is embedded into the container image.

If you are already familiar with running etcd clusters, you may prefer to run
etcd yourself. Doing so will give you greater flexibility in how and where it is
deployed, allow custom tuning, and may be easier to integrate into operational
tools and processes.

## Deploying compute-only nodes

As your cluster scales, you may wish to reserve specific nodes for storage and
other nodes for application workloads.

By default, StorageOS nodes both present and consume storage
 (`storageos.com/deployment=mixed`). You can specify that a node should only
 consume storage by setting the `storageos.com/deployment=computeonly` label.

 You can specify that a node should only consume storage by setting the
 `storageos.com/deployment=computeonly` label on the node. This can be done by
 setting the label on the StorageOS container at startup
 (LABELS='storageos.com/deployment=computeonly'), or by applying the label once
 it is running. Note that volumes must be drained off the node first.

## Rules

Rules are used for managing data policy and placement using StorageOS features
such as replication, QoS and compression.

Rules are defined using labels and selectors. When a volume is created, the
rules are evaluated to determine whether to apply the action.

An example business requirement might be that all production volumes are
replicated twice. This would be defined with a selector `env==prod`, and the
action would be to add the label `storageos.com/replicas=2`.

Rules can be created with the CLI or Web UI. See [Rules]({%link
_docs/operations/rules.md %}) for details.
