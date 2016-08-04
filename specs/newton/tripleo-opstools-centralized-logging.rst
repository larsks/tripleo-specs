..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

========================================
Enable deployment of centralized logging
========================================

https://blueprints.launchpad.net/tripleo/+spec/tripleo-opstools-centralized-logging

TripleO should be deploying an out-of-the-box centralized logging
solution to serve the overcloud.

Problem Description
===================

With a complex distributed system like OpenStack, identifying and
diagnosing a problem may require tracking a transaction across many
different systems and many different logfiles.  In the absence of a
centralized logging solution, this process is frustrating to both new
and experienced operators and can make even simple problems hard to
diagnose.

Proposed Change
===============

Overview
--------

The Fluentd_ service will be deployed in log collecting mode as a
composable service on all nodes in the overcloud stack by default.
Each composable service, when appropriate, will have it's own Fluentd
configuration.

.. _fluentd: http://www.fluentd.org/

A centralized logging system running Kibana_, Elasticsearch_ and
Fluentd will be deployed on dedicated nodes to provide log
aggregation and analysis.  This will be deployed in a dedicated Heat
stack that is separate from the overcloud stack.

.. _kibana: https://www.elastic.co/products/kibana
.. _elasticsearch: https://www.elastic.co/

Summary of use cases
--------------------

1. Elasticsearch, Kibana and Fluentd log relay/transformer deployed as
   a separate Heat stack in the overcloud stack; Fluentd log
   collecting agent deployed on each overcloud node

2. ElasticSearch, Kibana and Fluentd log relay/transformer deployed in
   external infrastructure; Fluentd deployed on each overcloud node

Alternatives
------------

None

Security Impact
---------------

Data collected from the logs of OpenStack services can contain
sensitive information:

- Communication between the
  fluentd agent and the log aggregator should be protected with SSL.

- Access to the Kibana UI must have at least basic HTTP
  authentication.

- ElasticSearch should only allow collections over ``localhost``.

Other End User Impact
---------------------

None

Performance Impact
------------------

Additional resources will be required for running Fluentd on overcloud nodes.  Log traffic from the overcloud nodes to the log aggregator will consume some bandwidth.

Other Deployer Impact
---------------------

- Fluentd will be deployed by default on all overcloud nodes except the monitoring node.
- New EFK parameters:

  TODO

- New parameters for log collection setting for each composable service:

  TODO

Developer Impact
----------------

Support for the new node type should be implemented for tripleo-quickstart.

Implementation
==============

Assignee(s)
-----------

Martin Mágr <mmagr@redhat.com>
Lars Kellogg-Stedman <lars@redhat.com>

Work Items
----------

- puppet-tripleo profile for fluentd service
- tripleo-heat-templates composable role for FluentD collector deployment
- tripleo-heat-templates composable role for FluentD aggregator deployment
- tripleo-heat-templates composable role for ElasticSearch deployment
- tripleo-heat-templates composable role for Kibana deployment
- Sync current undercloud support with new features in overcloud
- Support for logging node in tripleo-quickstart

Dependencies
============

- Puppet module for Fluentd: `puppet-fluentd` [1]
- Puppet module for ElasticSearch `elasticsearch-elasticsearch` [2]
- Puppet module for Kibana [?]
- CentOS Opstools SIG repo

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

[1] https://forge.puppet.com/srf/fluentd
[2] https://forge.puppet.com/elasticsearch/elasticsearch
