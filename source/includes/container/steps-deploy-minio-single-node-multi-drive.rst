1) Pull the Latest Stable Image of MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following command pulls the latest MinIO server container image from the quay.io.
You can alternatively pull the image from DockerHub.

.. tab-set::

   .. tab-item:: Podman

      **quay.io**

      .. code-block:: shell
         :class: copyable

         podman pull quay.io/minio/minio

      **DockerHub**

      .. code-block:: shell
         :class: copyable

         podman pull docker://minio/minio

   .. tab-item:: Docker

      **quay.io**

      .. code-block:: shell
         :class: copyable

         docker pull quay.io/minio/minio

      **DockerHub**

      .. code-block:: shell
         :class: copyable

         docker pull docker://minio/minio

2) Create the Environment Variable File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create an environment variable file at ``/etc/default/minio``.
The MinIO Server container can use this file as the source of all :ref:`environment variables <minio-server-environment-variables>`.

The following example provides a starting environment file:

.. code-block:: shell
   :class: copyable

   # MINIO_ROOT_USER and MINIO_ROOT_PASSWORD sets the root account for the MinIO server.
   # This user has unrestricted permissions to perform S3 and administrative API operations on any resource in the deployment.
   # Omit to use the default values 'minioadmin:minioadmin'.
   # MinIO recommends setting non-default values as a best practice, regardless of environment

   MINIO_ROOT_USER=myminioadmin
   MINIO_ROOT_PASSWORD=minio-secret-key-change-me

   # MINIO_VOLUMES sets the storage volumes or paths to use for the MinIO server.
   # The specified path uses MinIO expansion notation to denote a sequential series of drives between 1 and 4r inclusive.
   # All drives or paths included in the expanded drive list must exist *and* be empty or freshly formatted for MinIO to start successfully.

   MINIO_VOLUMES="/data-{1...4}"

   # MINIO_SERVER_URL sets the hostname of the local machine for use with the MinIO Server
   # MinIO assumes your network control plane can correctly resolve this hostname to the local machine

   # Uncomment the following line and replace the value with the correct hostname for the local machine.

   #MINIO_SERVER_URL="http://minio.example.net"

Include any other environment variables as required for your local deployment.

3) Create and Run the Container
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Select the container management interface of your choice for the relevant command syntax.

.. tab-set::

   .. tab-item:: Podman

      Copy the command to a text file for further modification.

      .. code-block:: shell
         :class: copyable

         podman run -dt                                  \
           -p 9000:9000 -p 9090:9090                     \
           -v PATH1:/data-1                              \
           -v PATH2:/data-2                              \
           -v PATH3:/data-3                              \
           -v PATH4:/data-4                              \
           -v /etc/default/minio:/etc/config.env         \
           -e "MINIO_CONFIG_ENV_FILE=/etc/config.env"    \
           --name "minio_local"                          \
           minio server --console-address ":9090"

      The following table describes each line of the command and provides additional configuration instructions:

      .. list-table::
         :header-rows: 1
         :widths: 40 60
         :width: 100%

         * - Line
           - Description

         * - ``podman run -dt``
           - Directs Podman to create and start the container as a detached (``-d``) background process with a pseudo-TTY (``-t``).
             This allows the container to run in the background with an open TTY for bash-like access.

         * - ``-p 9000:9000 -p 9090:9090``
           - Binds the ports ``9000`` and ``9090`` on the local machine to the same ports on the container.
             This allows access to the container through the local machine.

         * - ``-v PATHx:/mnt/data-x``
           - Binds the storage volume ``PATH`` on the local machine to the ``/mnt/data-x`` path on the container.
             Replace this value with the full path to each sequential storage volume or folder on the local machine.
             For example:

             **Linux or MacOS** : ``/mnt/data-1/``
             **Windows** : ``D:\data\``

             Include additional ``-v`` parameters such that one mount exists for each drive specified to the :envvar:`MINIO_VOLUMES` value in the environment file.

         * - ``-v /etc/default/minio:/etc/config.env``
           - Mounts the environment file created in the previous step to the ``/etc/config.env`` path on the Container.
             The MinIO Server can use this environment file for configuration.
             
         * - ``-e "MINIO_CONFIG_ENV_FILE=/etc/config.env"``
           - Sets a MinIO environment variable pointing to the container-mounted path of the environment file.

         * - ``--name "minio_local"``
           - Sets a custom name for the container. 
             Omit this value to allow Podman to automatically generate a container name.
             You can replace this value to best reflect your requirements.

         * - ``minio server --console-address ":9090"``
           - Starts the MinIO server using the ``minio:minio`` image pulled from an earlier step.
             The :mc:`minio server --console-address ":9090" <minio server --console-address>` option directs the server to set a static port for the MinIO Console Web Interface.
             This option is *required* for containerized environments.

             If you modify this value, ensure you set the proper port mapping using the ``-p`` flag to Podman to ensure traffic forwarding between the local host and the container.

      Specify any other :podman-docs:`options <markdown/podman-run.1.html>` to ``podman run`` as necessary for your local environment.

   .. tab-item:: Docker

      Copy the command to a text file for further modification.

      .. code-block:: shell
         :class: copyable

         docker run -dt                                  \
           -p 9000:9000 -p 9090:9090                     \
           -v PATH1:/data-1                              \
           -v PATH2:/data-2                              \
           -v PATH3:/data-3                              \
           -v PATH4:/data-4                              \
           -v /etc/default/minio:/etc/config.env         \
           -e "MINIO_CONFIG_ENV_FILE=/etc/config.env"    \
           --name "minio_local"                          \
           minio server --console-address ":9090"

      The following table describes each line of the command and provides additional configuration instructions:

      .. list-table::
         :header-rows: 1
         :widths: 40 60
         :width: 100%

         * - Line
           - Description

         * - ``docker run -dt``
           - Directs Docker to create and start the container as a detached (``-d``) background process with a pseudo-TTY (``-t``).
             This allows the container to run in the background with an open TTY for bash-like access.

         * - ``-p 9000:9000 -p 9090:9090``
           - Binds the ports ``9000`` and ``9090`` on the local machine to the same ports on the container.
             This allows access to the container through the local machine.

         * - ``-v PATH:/data/minio``
           - Binds the storage volume ``PATH`` on the local machine to the ``/data`` path on the container.
             Replace this value with the full path to each sequential storage volume or folder on the local machine.
             For example:

             **Linux or MacOS** : ``/mnt/data-1/``
             **Windows** : ``D:\data\``

             Include additional ``-v`` parameters such that one mount exists for each drive specified to the :envvar:`MINIO_VOLUMES` value in the environment file.

         * - ``-v /etc/default/minio:/etc/config.env``
           - Mounts the environment file created in the previous step to the ``/etc/config.env`` path on the Container.
             The MinIO Server can use this environment file for configuration.
             
         * - ``-e "MINIO_CONFIG_ENV_FILE=/etc/config.env"``
           - Sets a MinIO environment variable pointing to the container-mounted path of the environment file.

         * - ``--name "minio_local"``
           - Sets a custom name for the container. 
             Omit this value to allow Podman to automatically generate a container name.
             You can replace this value to best reflect your requirements.

         * - ``minio server --console-address ":9090"``
           - Starts the MinIO server using the ``minio:minio`` image pulled from an earlier step.
             The :mc:`minio server --console-address ":9090" <minio server --console-address>` option directs the server to set a static port for the MinIO Console Web Interface.
             This option is *required* for containerized environments.

             If you modify this value, ensure you set the proper port mapping using the ``-p`` flag to Docker to ensure traffic forwarding between the local host and the container.

      Specify any other `options <https://docs.docker.com/engine/reference/commandline/run/>`__ to ``docker run`` as necessary for your local environment.

      For running Docker in Rootless mode, you may need to set the following additional Docker CLI options:

      **Linux** - ``--user $(id -u):$(id -g)`` - directs the container to run as the currently logged in user.
      **Windows** - ``--security-opt "credentialspec=file://path/to/file.json"`` - directs the container to run using a Windows `Group Managed Service Account <https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts>`_.

      Once you have applied any further customizations to the command, run it in your preferred terminal or shell environment.
      The command should return a unique ID for the created container.

4) Validate the Container Status
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: Podman

      Run the following command to retrieve logs from the container.
      Replace the container name with the value specified to ``--name`` in the previous step.

      .. code-block:: shell
         :class: copyable

         podman logs minio

      The command should return output similar to the following:

   .. tab-item:: Docker

      Run the following command to retrieve logs from the container.
      Replace the container name with the value specified to ``--name`` in the previous step.

      .. code-block:: shell
         :class: copyable

         docker logs minio

      The command should return output similar to the following:

.. code-block:: none

   Formatting 1st pool, 1 set(s), 4 drives per set.
   WARNING: Host local has more than 4 drives of set. A host failure will result in data becoming unavailable.
   MinIO Object Storage Server
   Copyright: 2015-2022 MinIO, Inc.
   License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
   Version: RELEASE.2022-07-08T00-05-23Z (go1.18.3 linux/amd64)

   Status:         1 Online, 0 Offline. 
   API: http://10.0.2.100:9000  http://127.0.0.1:9000       
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 
   Console: http://10.0.2.100:9090 http://127.0.0.1:9090    
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 

   Command-line: https://docs.min.io/docs/minio-client-quickstart-guide
      $ mc alias set myminio http://10.0.2.100:9000 myminioadmin minio-secret-key-change-me

   Documentation: https://docs.min.io

Note that the ``API`` and ``CONSOLE`` blocks may include the network interfaces for the container.
Clients outside of the container network cannot access the MinIO API or Console using these addresses.

5) Connect to the MinIO Service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: MinIO Web Console

      You can access the MinIO Web Console by entering http://localhost:9090 in your preferred browser.
      Any traffic to the MinIO Console port on the local host redirects to the container.

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
      Use a hostname or IP address for your local machine along with the S3 API port ``9000`` to access the MinIO deployment.
      Any traffic to that port on the local host redirects to the container.

      .. code-block:: shell
         :class: copyable

         mc alias set http://localhost:9000 myminioadmin minio-secret-key-change-me

      Replace ``myminioadmin`` and ``minio-secret-key-change-me`` with the :envvar:`MINIO_ROOT_USER` and :envvar:`MINIO_ROOT_PASSWORD` values in the environment file specified to the container.

      The command should return success if the container is running and accessible at the specified port.

      You can then interact with the container using any :mc:`mc` command.
      If your local host firewall permits external access to the MinIO S3 API port, other hosts on the same network can access the MinIO deployment using the IP or hostname for your local host.