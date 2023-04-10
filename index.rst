:tocdepth: 1

.. sectnum::

Abstract
========

Foreman administrative procedures and usage notes

Terminology
===========

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in `BCP 14 <https://www.rfc-editor.org/info/bcp14>`_ [`RFC2119 <https://datatracker.ietf.org/doc/html/rfc2119>`_] [`RFC8174 <https://datatracker.ietf.org/doc/html/rfc8174>`_] when, and only when, they appear in all capitals, as shown here.

Host Provisioning
=================

Networking
----------

On the Hosts -> Interfaces tab, all managed interfaces (this excludes interfaces which foreman may be reported as having an interface but which are unmanaged via puppet/foreman such as the docker created `virbr0`) with an IP address assigned SHALL have an name set (FQDN in foreman speak).

.. note::

   Bridge, Trunk, etc. interfaces without an IP address assigned SHALL NOT have an FQDN set in foreman.

The main reason to set an FQDN name via foreman on secondary interfaces is to create DNS records for those IPs. The existence of DNS records avoids the need to hard code IP addresses in configuration and often aids in debugging, particularly for `PTR` records. E.g.

.. code-block:: bash

   ~ $ dig +short -x 139.229.170.33
   auxtel-mcm-1400.cp.lsst.org.

The format of these names shall be:

- The primary or main host interface is `<hostname>.<site>.lsst.org`
- The BMC interface is `<hostname>-bmc.<site>.lsst.org`
- All other "secondary" interface(s) are `<hostname>-<vlan id>.<site>.lsst.org`

.. figure:: /_static/foreman_network-auxtel-mcm.cp.lsst.org.png
   :name: fig-foreman-network
   :alt: Image of the Foreman Host Interfaces tab

DDS Interfaces
^^^^^^^^^^^^^^

If the host has a "dds" or "sal" interface, with an IP assigned via foreman, than there SHALL be a DNS `CNAME` record in the format `<hosthame>-dds.<site>.lsst.org` which points to the appropriate `<hostname>-<vlan id>.<site>.lsst.org` `A` record. E.g.

.. code-block:: bash

   ~ $ dig +short auxtel-mcm-dds.cp.lsst.org
   auxtel-mcm-1400.cp.lsst.org.
   139.229.170.33

.. note::

   It is not currently possible to create `CNAME` records via foreman.  The route53 console or API will have to be used to create the `CNAME` manually.
