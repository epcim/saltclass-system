
Generic system model for SaltClass based deployment
===================================================

This repo is based on existing reclass-model (https://github.com/Mirantis/reclass-system-salt-model)

Contains default pillar data for various systems/apps. Use along with pillar data on
- service level (enbeded in formula under `/metadata`)
- cluster level (an class override for individual deployment)

Expected SaltClass root structure:

.. code-block:: shell

  /srv/salt/env  # installed formulas
  /srv/salt/saltclass/
    - nodes
    - services/
                 # link installed formulas `<formula>/metadata/service`
                 #
                 # SaltClass fixup:
                 # for i in $(grep -r -e '^applications:' -e '^parameters:' -l ${SALT_CLASS_SERVICE:-/usr/share/salt-formulas/reclass/service}); do
                 #   sed -i 's/applications:/states:/g;s/parameters:/pillars:/g' $i;
                 # done
    - classes/
       - system  # (this repo as subrepository)
       - cluster # (your overrides)


SaltClass
=========

Added to SaltStack Salt by PR: https://github.com/saltstack/salt/pull/42349.

Configuration
-------------

Directly to master.conf:

.. code-block:: yaml

  ext_pillar:
    - saltclass:
      - path: /srv/salt/saltclass

  master_tops:
    saltclass:
      path: /srv/salt/saltclass


With the `salt-formula-salt saltclass`_ pillar:

.. _salt-formula-salt saltclass: https://github.com/salt-formulas/salt-formula-salt/blob/master/tests/pillar/master_single_extpillars.sls#L55

.. code-block:: yaml

  salt:
    pillar:
      engine: composite
      saltclass:
        path: /srv/salt/saltclass

Examples
--------

Basic jinja + grains example

.. code-block:: yaml

  # /srv/salt/saltclass/nodes/minion.domain.yml
  environment: prod
  classes:
  {% for class in ['default', 'app1'] %}
    - {{ class }}
  {% endfor %}
    - {{ __grains__['os'] }}

  pillars:
    default:
      network:
        dns:
          srv3: 192.168.1.1
      os: {{ __grains__['oscodename'] }}

