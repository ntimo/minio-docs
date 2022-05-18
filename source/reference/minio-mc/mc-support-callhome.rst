=======================
``mc support callhome``
=======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 1

.. mc:: mc support callhome

Description
-----------

The :mc-cmd:`mc support callhome` command configures data upload settings for deployments registered with the MinIO subscription network, |SUBNET|.

This functionality is disabled by default, even for clusters registered with SUBNET.
   
:mc-cmd:`mc support callhome` has two subcommands:

:mc-cmd:`~mc support callhome set`
   Configure a deployment's ``callhome`` settings for connecting to SUBNET.

:mc-cmd:`~mc support callhome get`
   Output a deployment's ``callhome`` settings for connecting to SUBNET.

Examples
--------

Enable ``callhome`` Logs
~~~~~~~~~~~~~~~~~~~~~~~~

Enable logs callhome for cluster with the alias ``minio1``.
When enabled for a deployment registered to SUBNET, MinIO sends logs to SUBNET in real time.

.. code-block:: shell
   :class: copyable
 
   mc support callhome set minio1 logs=on

Disable ``callhome`` Logs
~~~~~~~~~~~~~~~~~~~~~~~~~

Disable sending realtime logs to SUBNET for a cluster registed to SUBNET with an :ref:`alias <alias>` of ``minio1``.

.. code-block:: shell
   :class: copyable

   mc support callhome set minio1 logs=off


Display Current ``callhome`` Settings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Display ``callhome`` data for a cluster with the alias ``minio1`` to SUBNET.

.. code-block:: shell
   :class: copyable
 
   mc support callhome get minio1

Dsiplay Current Logs Setting for ``callhome``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Display whether the current callhome configuration allows for sending logs to SUBNET for a cluster with alias ``minio1``.

.. code-block:: shell
   :class: copyable

   mc support callhome get minio1 logs

Syntax
------

.. mc-cmd:: set
   :fullpath:

Configure ``callhome`` settings for a cluster.
The cluster must already be registred for SUBNET.

.. code-block:: shell
               
   mc support callhome set          \
                     ALIAS          \
                     logs=[on|off]

.. mc-cmd:: get
   :fullpath:

Retrieve ``callhome`` settings for a cluster.

.. code-block:: shell
               
   mc support callhome get    \
                       ALIAS  \
                       logs
            

Parameters
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   The :ref:`alias <alias>` of the MinIO deployment.

.. mc-cmd:: logs
   :required:

   For :mc-cmd:`~mc support callhome set`, use ``logs=on`` or ``logs=off`` to define whether to turn the realtime submission of logs to SUBNET ``on`` or ``off``.

   For :mc-cmd:`~mc support callhome get`, ``logs`` takes no value, and outputs whether the alias is currently configured to send logs.
       

Global Flags
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

