
.. _compatibility:

====================
Compatibility Guide
====================

This guide is aimed at OpenNebula 5.13.x users and administrators who want to upgrade to the latest version. The following sections summarize the new features and usage changes that should be taken into account, or are prone to cause confusion. You can check the upgrade process in the :ref:`corresponding section <upgrade>`.

Visit the :ref:`Features list <features>` and the `Release Notes <https://opennebula.io/use/>`__ for a comprehensive list of what's new in OpenNebula 5.13.

HTTP_PROXY and XMLRPC API
=========================
Scheduler clears the http_proxy environment variable. If your oned xml-rpc API is behind a HTTP_PROXY you need to update ``sched.conf`` and set the right value there.

Ruby API
========

Some functions has been moved from API to new API extensions:

- save_as_template

To be able to use them, you need to extend from the extensions file:

.. code::

    require 'opennebula/virtual_machine_ext'

    vm_new = VirtualMachine.new(VirtualMachine.build_xml(@pe_id), @client)
    vm_new.extend(VirtualMachineExt)

Distributed Edge Provisioning
=============================

Information about provision is stored in a JSON document. For this reason, the ERB evaluation must be done using the variable ``@body['provision']``.

To access to infrastructure resources, just access to key ``infrastrcuture`` following by the object, e.g:

.. code::

    @body['provision']['infrastructure']['datastores'][0]['id']

To access to resources, just access to key ``resource`` following by the object, e.g:

.. code::

    @body['provision']['resource']['images'][0]['id']

Check more information :ref:`here <ddc_virtual>`.

In provision template, the attribute ``driver`` has be changed by ``provider``.

The driver EC2 has been renamed to AWS to follow Terraform provider name. Consequently, the keys has been renamed in the following way:

- ec2_access -> access_key
- ec2_secret -> secret_key
- region_name -> region

Provision drivers has been changed by Terraform, so the following commands are no longer avaialble:

- ``oneprovision host resume``
- ``oneprovision host poweroff``
- ``oneprovision host reboot``
- ``oneprovision host reboot --hard``

Datastore Driver Changes
=============================

   - Now the CP datastore action needs to return also de format of the file copied (e.g raw or qcow2). This way, when a file is uploaded by the user, the format of the file is automatically retrieved avoiding user mistakes.

   - The ``DRIVER`` and ``FSTYPE`` attributes are deprecated and they won't be taking into account any more.

.. note:: The ``DRIVER`` attribute will be set automatically for each disk.

.. _compatibility_kvm:

KVM Driver Defaults Changed
===========================

KVM driver comes with new defaults, which better reflect modern use of this technology (i.e., leverage paravirtualized interfaces or rely more on QEMU guest agent). Consult the current defaults in following vanilla configuration files provided with the OpenNebula:

- ``/etc/one/vmm_exec/vmm_exec_kvm.conf``
- ``/var/lib/one/remotes/etc/vmm/kvm/kvmrc``

Default path to QEMU emulator (parameter ``EMULATOR`` in ``/etc/one/vmm_exec/vmm_exec_kvm.conf``) has changed from distribution specific-path to an unified symbolic link ``/usr/bin/qemu-kvm-one``, which is created on hypervisors during the installation of KVM node package, and points to the QEMU binary of each node operating system.

.. _compatibility_pkg:

Distribution Packages Renamed
=============================

Names of main distribution packages were unified across the distributions to eliminate differences and avoid confusion. Users might need to update their custom scripts (e.g., own Ansible installation tasks, Dockerfiles) to deal with new packages. Upgrades of existing deployments shouldn't be negatively affected as the deprecations are automatically handled by the package managers.

On CentOS/RHEL renamed package

* **opennebula** (formerly CLI tools) to **opennebula-tools**
* **opennebula-server** (formerly OpenNebula daemon and scheduler) to **opennebula**
* **opennebula-ruby** to **opennebula-libs**

On Debian/Ubuntu renamed package

* **opennebula-node** to **opennebula-node-kvm**
* **ruby-opennebula** to **opennebula-libs**

See the curent :ref:`list of shipped packages <packages>`.
