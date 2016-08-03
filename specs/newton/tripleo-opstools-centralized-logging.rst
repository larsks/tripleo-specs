..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

========================================
Enable deployment of centralized logging
========================================

https://blueprints.launchpad.net/tripleo/+spec/tripleo-opstools-centralized-logging

TripleO should be deploying out-of-the-box centralized logging solution to serve
the overcloud.

Problem Description
===================

Currently there is no such feature implemented.
Proposed Change
===============

Overview
--------

The Fluentd service will be deployed in log collecting mode as a composable
service on the overcloud stack by default. Each composable service will have
it's own fluentd configuration (if it is possible), which will ensure that
correct logs are collected on correct overcloud nodes.

There will be implemented a possibility to deploy Kibana, Elasticsearch and
FluentD in log relay/transformer mode on a stand alone node deployed
by the undercloud dedicated for monitoring and centralized logging purposes.

The monitoring node will be deployed as a separate Heat stack to the overcloud
stack using Puppet and composable roles for required services.

FluentD log collection agents deployment support should be flexible enough
to enable connection to external datastore infrastructure or with Elasticsearch
deployed on the dedicated overcloud node.

Summary of use cases:

1.elasticsearch, kibana and fluentd log relay/transformer deployed in external
infrastructure; fluentd deployed on each overcloud node

2. elasticsearch, kibana and fluentd log relay/transformer deployed as
a separate Heat stack in the overcloud stack; fluentd log collecting agent
deployed on each overcloud node

Alternatives
------------

None

Security Impact
---------------

Data collected from logs of OpenStack services can contain sensitive information
and so Kibana should be configured at least with basic http auth. Only local
connections should be allowed for Elasticsearch. We should also consider running
FluentD communication with SSL enabled.

Other End User Impact
---------------------

None

Performance Impact
------------------

Additional resources will be required for running FluentD on overcloud nodes.

Other Deployer Impact
---------------------

* FluentD will be deployed by default on all overcloud nodes except the monitoring node.
* New EFK parameters:

* New parameters for log collectiobn setting for each composable service:

    * For example for service nova-compute LogCollectionNovaCompute, which will default to 'overcloud-nova-compute'


Developer Impact
----------------

Support for new node type should be implemented for tripleo-quickstart.

Implementation
==============

Assignee(s)
-----------

Martin Mágr <mmagr@redhat.com>

Work Items
----------

* puppet-tripleo profile for Sensu services
* puppet-tripleo profile for Uchiwa service
* tripleo-heat-templates composable role for sensu-client deployment
* tripleo-heat-templates composable role for sensu-server deployment
* tripleo-heat-templates composable role for sensu-api deployment
* tripleo-heat-templates composable role for uchiwa deployment
* Sync current undercloud support with new features in overcloud
* Support for monitoring node in tripleo-quickstart

Dependencies
============

* Puppet module for Sensu services: sensu-puppet [1]
* Puppet module for Uchiwa: puppet-uchiwa [2]
* CentOS Opstools SIG repo [3]

Testing
=======

Sensu client deployment will be tested by current TripleO CI as soon as
the patch is merged, as it will be deployed by default.

We should consider creating CI job for deploying overcloud with monitoring
node to test the rest of the monitoring components.

Documentation Impact
====================

Process of creating new node type and new options will have to be documented.

References
==========

[0] https://sensuapp.org/docs/latest/reference/checks.html#subscription-checks
[1] https://github.com/sensu/sensu-puppet
[2] https://github.com/Yelp/puppet-uchiwa
[3] https://wiki.centos.org/SpecialInterestGroup/OpsTools
