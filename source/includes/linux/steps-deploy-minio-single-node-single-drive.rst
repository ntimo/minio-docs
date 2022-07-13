1) Download the MinIO Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-binary-desc
   :end-before: end-install-minio-binary-desc

2) Create the ``systemd`` Service File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-systemd-desc
   :end-before: end-install-minio-systemd-desc

3) Create the Environment Variable File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create an environment variable file at ``/etc/default/minio``.
The MinIO Server can use this file as the source of all :ref:`environment variables <minio-server-environment-variables>`.

The following example provides a starting environment file:

.. code-block:: shell
   :class: copyable

   # MINIO_ROOT_USER and MINIO_ROOT_PASSWORD sets the root account for the MinIO server.
   # This user has unrestricted permissions to perform S3 and administrative API operations on any resource in the deployment.
   # Omit to use the default values 'minioadmin:minioadmin'.
   # MinIO recommends setting non-default values as a best practice, regardless of environment

   MINIO_ROOT_USER=minioadmin
   MINIO_ROOT_PASSWORD=minio-secret-key-change-me

   # MINIO_VOLUMES sets the storage volume or path to use for the MinIO server.

   MINIO_VOLUMES="/mnt/data"

   # MINIO_SERVER_URL sets the hostname of the local machine for use with the MinIO Server
   # MinIO assumes your network control plane can correctly resolve this hostname to the local machine

   # Uncomment the following line and replace the value with the correct hostname for the local machine.

   #MINIO_SERVER_URL="http://minio.example.net"

Include any other environment variables as required for your local deployment.

4) Start the MinIO Service
~~~~~~~~~~~~~~~~~~~~~~~~~~

Issue the following command on the local host to start the MinIO |SNSD| deployment as a service:

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-start-service-desc
   :end-before: end-install-minio-start-service-desc

The ``journalctl`` output should resemble the following:

.. code-block:: shell

   Formatting 1st pool, 1 set(s), 1 drives per set.
   WARNING: Host local has more than 0 drives of set. A host failure will result in data becoming unavailable.
   MinIO Object Storage Server
   Copyright: 2015-2022 MinIO, Inc.
   License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
   Version: RELEASE.2022-07-08T00-05-23Z (go1.18.3 linux/amd64)

   Status:         1 Online, 0 Offline. 
   API: http://192.168.2.100:9000  http://127.0.0.1:9000       
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 
   Console: http://192.168.2.100:9090 http://127.0.0.1:9090    
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 

   Command-line: https://docs.min.io/docs/minio-client-quickstart-guide
      $ mc alias set myminio http://10.0.2.100:9000 myminioadmin minio-secret-key-change-me

   Documentation: https://docs.min.io

The ``API`` block lists the network interfaces and port on which clients can access the MinIO S3 API.
The ``Console`` block lists the network interfaces and port on which clients can access the MinIO Web Console.

5) Connect to the MinIO Service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: MinIO Console

      You can access the MinIO Console by entering any of the hostnames or IP addresses from the MinIO server ``Console`` block in your preferred browser, such as http://localhost:9090.

      Log in with the :envvar:`MINIO_ROOT_USER` and :envvar:`MINIO_ROOT_PASSWORD` configured in the environment file specified to the container.

      .. image:: /images/minio-console/minio-console.png
         :width: 600px
         :alt: MinIO Console Dashboard displaying Monitoring Data
         :align: center

      You can use the MinIO Console for general administration tasks like Identity and Access Management, Metrics and Log Monitoring, or Server Configuration. Each MinIO server includes its own embedded MinIO Console.

      If your local host firewall permits external access to the Console port, other hosts on the same network can access the Console using the IP or hostname for your local host.

   .. tab-item:: MinIO CLI (mc)

      You can access the MinIO deployment over a Terminal or Shell using the :ref:`MinIO Client <minio-client>` (:mc:`mc`).
      See :ref:`MinIO Client Installation Quickstart <mc-install>` for instructions on installing :mc:`mc`.

      Create a new :mc-cmd:`alias <mc alias set>` corresponding to the MinIO deployment. 
      Specify any of the hostnames or IP addresses from the MinIO Server ``API`` block, such as http://localhost:9000.

      .. code-block:: shell
         :class: copyable

         mc alias set http://localhost:9000 myminioadmin minio-secret-key-change-me

      Replace ``myminioadmin`` and ``minio-secret-key-change-me`` with the :envvar:`MINIO_ROOT_USER` and :envvar:`MINIO_ROOT_PASSWORD` values in the environment file specified to the container.

      You can then interact with the container using any :mc:`mc` command.
      If your local host firewall permits external access to the MinIO S3 API port, other hosts on the same network can access the MinIO deployment using the IP or hostname for your local host.