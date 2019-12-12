.. Zoning Service API documentation master file, created by
   sphinx-quickstart on Thu Dec 12 09:12:02 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Zoning Service API
==============================================

.. toctree::
   :caption: Site Map
   :maxdepth: 2
   :numbered:

.. contents:: Table of Contents
   :depth: 3

Overview
----------

The *Zoning Service* provides access to a collection of zones, with a zone being a single named geographic area.

A *zone* is identified by a name and type:

+---------------------------------+-------------+
|Name                             | Type        |
+=================================+=============+
|France.lle-de-France.Paris.Paris | AA8         |
+---------------------------------+-------------+

A zone consists of a group of morton tiles at various levels with level 14 being the lowest level of tile. Level 14 tiles that occur at the border of a zone are accompanied by geometry defining the zone border within that tile.

The Zoning Service is currently implemented in Java as a **JAX-WS** service. The data storage is implemented using **HBase**, although the storage mechanism is abstracted via the **ZoneRepository** interface.

Service API
=============

.. list-table::
   :widths: 5, 5, 10, 10, 70
   :header-rows: 1

   * - Method
     - Endpoint
     - Description
     - Example Request
     - Example Response
   * - GET
     - /status
     - Returns application status.
     -
     -
   * - GET
     - /info
     - Display data available in the web service (contents of this table).
     -
     -
   * - GET
     - /zone/{type}/{zoneName}?version=<value>
     - Morton tiles, complete or partial, that the zone contains.
     - /zone/AA7/Belgie.VlaamsGewest.Oost-Vlaanderen.Gent
     - ::

        OK
        {"name" : "België.Vlaams Gewest.Oost-Vlaanderen.Gent",
        "type" : "AA7",
        "version" : "12.4.1",
        "geometry" : "",
        "tiles" : [[CPEQAE:14,235422661,"POLYGON (((34216750 508996583, 34277344 508996583, ...)))"],[CPEQAE:14,235422681,""]... }
        The morton tiles that are completely in the zone do not have polygons in the response.
        The morton tiles that are partially in the zone do have geometry in the response.
   * - GET
     - /zone/{type}/{zoneName}/related?version=<value>&relatedType=<value>
     - Gets the JSON representing the ZoneInfos describing zones that are geographically related to the target zone. This method can be used to find spatial relationships (i.e. within, contains) between zones of different types.
     - /zone/AA7/België.Vlaams Gewest.Oost-Vlaanderen.Gent
     - ::

        OK
        <stream of zones>
   * - GET
     - /zone/{type}/{zoneName}/tiles?version=<value>&level=<value>
     - Gets zone tiles.
     - /zone/AA7/België.Vlaams Gewest.Oost-Vlaanderen.Gent
     - ::

        OK
        <stream of tiles>
   * - GET
     - /zone/{type}/{zoneName}/tiles/{density}?version=<value>
     - Gets zone tiles for specified density
     - /zone/AA7/België.Vlaams Gewest.Oost-Vlaanderen.Gent
     - ::

        OK
        <stream of density tiles>
   * - GET
     - /zoneview/{type}/{zoneName}?version=<value>&level=<value>
     - Shows page with zone view (visualization of multi-polygon).
     - /zoneview/AA7/België.Vlaams Gewest.Oost-Vlaanderen.Gent
     - ::

        OK
        <html>
   * - GET
     - /zonesforgeometry/{wkt}?version=<value>&type=<value>
     - Retrieves zones for passed WKT geometry
     - /zonesforgeometry/
     - ::

        OK
        [{"name":"BEL","type":"TIFCOUNTRY","version":"12.7.2"},
        {"name":"FRA","type":"TIFCOUNTRY","version":"12.7.2"},
        {"name":"BEL","type":"TIFDATASET","version":"12.7.2"},
        {"name":"F19","type":"TIFDATASET","version":"12.7.2"}]
   * - GET
     - /zonesintile/{tileLevel14}?version=<value>&type=<value>&level=<value>
     - Zones that contain the Morton tile of this ID; Default level is 14 (param is not required)
     - /zonesintile/14713859
     - ::

        OK
        [{"name":"BEL","type":"TIFCOUNTRY","version":"12.7.2"},
        {"name":"FRA","type":"TIFCOUNTRY","version":"12.7.2"},
        {"name":"BEL","type":"TIFDATASET","version":"12.7.2"},
        {"name":"F19","type":"TIFDATASET","version":"12.7.2"}]
   * - GET
     - /zonesforkeyword/{keyword}?version=<value>&type=<value>
     - Returns the breadcrumbs that contain the keyword.
     - /zonesforkeyword/ledeberg
     - ::

        OK
        [{"name":"BEL","type":"TIFCOUNTRY","version":"12.7.2"}, ... ]
   * - GET
     - /zonesforcoord/{longitude}/{latitude}?version=<value>&type=<value>
     - Zones on this location.
     - /zonesforcoord/23071290/488238795
     - ::

        OK
        [{"name":"BEL","type":"TIFCOUNTRY","version":"12.7.2"},
        {"name":"FRA","type":"TIFCOUNTRY","version":"12.7.2"},
        {"name":"BEL","type":"TIFDATASET","version":"12.7.2"},
        {"name":"F19","type":"TIFDATASET","version":"12.7.2"}]
   * - GET
     - /zoneversions/{type}/{zoneName}
     - All version of the given zone.
     - /zoneversions/TIFDATASET/AND
     - ::

        OK
        [{"name":"AND","type":"TIFDATASET","version":"12.4.1"},
        {"name":"AND","type":"TIFDATASET","version":"12.7.2"}]
   * - GET
     - /getrel/{timestamp}
     - Gets the release label for this timestamp (long).
     - /getrel/1317420000000
     - [CPEQAE:"11.10.1"]
   * - POST
     - /zone
     - Creates a new zone.
     - ::

        /zone
        Body:zoneJson
     - CREATED
   * - POST
     - /tiles/{density}
     - Adds zone density tiles.
     - ::

        /tiles/target
        Body: WKT
     - ::

        OK
        <stream of density tiles>



Parameter Values
---------------------

The following table provides additional details regarding the parameters.

.. list-table::


  * - Parameter
    - Format/Value
    - Example
  * - version
    - yy.M.d.HH.mm
    - "12.3.4" (represents 4/3/2012 0:00.00.000)
  * - version
    - (empty)
    - (displays the latest version)
  * - type
    - dataset
    - ?
  * - type
    - country
    - ?
  * - type
    - breadcrumbs
    - ?
  * - zonename
    -
    - "België.Vlaams Gewest.Oost-Vlaanderen.Gent"
  * - tileLevel14
    - morton tile level 14
    - 235026145
  * - keyword
    - any string to search on
    - "Ledeberg"
  * - longitude, latitude
    - Geographic Coordinate System (GCS)
    - 23071290, 488238795
  * - timestamp
    - long
    - 1317420000000 (long representation of date)

.. note::

   *Type* is taken from the type value in the zone info JSON files consumed by the *zone-create-tool*. There is no ENUM in the code.



Zone Definition Creator (2018)
===============================

The *Zone Definition Creator* uses WKT to create new zones in the Zoning Service.

.. seealso::

  `Zone definition zone creator <https://confluence.tomtomgroup.com/display/CMR/Zone+definition+zone+creator>`__

  `RMDSZone Creator/Functionality <http://help.tomtomgroup.com/ttpedia/wiki_tm/index.php/RMDSZOne_Creator/Functionality>`__

Legacy
========

Two Zone Service environments have existed together for some time now:

  - *Live Cava Zone Service* - stores mostly data set geometries.
  - *ContentOps Zone Service* - stores mostly zone geometries.

BULKQA (02.2019)
==================

*BulkQA* uses the ContentOps Zoning Service to perform QA validation. It's useful for the team because it supports skipping of empty tiles.

Contact `Slawomir Caban <Slawomir.Caban@tomtom.com>`__ for additional details.

.. note:: Original project administrators are `Patrick Syroit <Patrick.Syroit@tomtom.com>`__ and `Christel Fie <Christel.fie@tomtom.com>`__.

EMR Zone Evaluation
=====================

Zones are evaluated by the `EMR application <https://bitbucket.tomtomgroup.com/projects/PROD/repos/zoning-module/browse/zone-create-tool>`__.

Artifacts producted by the compilation are named as ``zone-create-tool-version_number-job.jar``.

This artifact is produced on EMR by the job `http://jenkins.maps-contentprocessing-prod.amiefarm.com:8080/view/BQA/job/BQA_Zone_Creator/ <http://jenkins.maps-contentprocessing-prod.amiefarm.com:8080/view/BQA/job/BQA_Zone_Creator/>`__

Additional notes regarding the job are as follows:

  - The job uploads the zone geometry as **WKT** to the **EMR Master** node.

  - After uploading the zone geometry, the job starts the ``yarn`` application and issues the commands:

::

    yarn --config /etc/hbase/conf jar /home/hadoop/zone-create-tool-version-job.jar zone-create-tool -i /home/hadoop/t -p aws_cpp_r2


The job uses the Hadoop command instead of ``yarn`` though the output is the same.

  - ``-- config`` is used to point to a custom configuration. Using the the default Hbase configuration ``/etc/hbase/conf`` prevents any issues when executing the application.

  - The main application code can be debugged by running the yarn command in the terminal. The output is then displayed on the screen.

  - The distributed functions log to Core node under ``/var/log/hadoop-yarn/containers``.

EMR Configuration
==================

EMR configuration is controlled by the terraform `https://bitbucket.tomtomgroup.com/projects/CPPINFRA/repos/cpp/browse/aws_profiles/maps-contentops/cppedit/zoningservice <https://bitbucket.tomtomgroup.com/projects/CPPINFRA/repos/cpp/browse/aws_profiles/maps-contentops/cppedit/zoningservice>`__

The service then uses the module defined at `https://bitbucket.tomtomgroup.com/projects/CPPINFRA/repos/cpp-modules/browse/zoning-service <https://bitbucket.tomtomgroup.com/projects/CPPINFRA/repos/cpp-modules/browse/zoning-service>`__

Notes on configuration
-------------------------

- The subnet is related to the VPC, which has DCHP options that contain ``domain-name = net-172-29-16-0-20.tt3.com``

- In the context of EMR machines, this leads to instantiation of the process ``dnsmasq --listen-address=127.0.0.1 --synth-doain=net=172-29-16-0-20.tt3.com,172.29.16.0/20,ip-`` which runs the local DNS server that dynamically assigns host names and domains for IPs in the subnet.

- The file ``/etc/resolve.conf`` defines the local DNS before the Amazon DNS, then all forward and reverse DNS queries for IPs from the subdomain are handled by this local DNS.

This setup causes problems for the zoning-service web application, which in communication with EMR receives host names reverse resolved to synth domain. Since the zoning-service application is running in the same subnet, it doesn't create the synth domain locally. This is resolved by adding the zoning-service web application to the ``node.eu-west-1-mapsco.maps-contentops.amiefarm.com`` domain as an entry in the ``/etc/resolve.conf``. The domain is handled by Consul.

When the zoning-service receives an address such as ``ip-172-29-25-51.net-172-29-16-0-20.tt3.com``, the domain suffix is added and the DNS attempts to resolve the address ``ip-172-29-25-51.net-172-29-16-0-20.tt3.com.node.eu-west-1-mapsco.maps-contentops.amiefarm.com``

Note that for this process to work, the Master and Core machines of the EMR cluster has to be registered in Consul under this kind of entry. This can be achived by downloading the Python script ``s3://maps-contentops/hadoop-aws/install_and_configure_consul.sh`` on the Master and Core machines and then running it. In addition, verify that the Route53 entry for ``hadoop.maps-contentops.amiefarm.com`` points to Master.

Glossary
=========

.. glossary::

   Morton tile
     A *Morton tile* is used to map multidimensional data to one dimension.

   Zone
     A *zone* refers to a specific geographic area.

Reference Links
================

- ContentOps Zoning Service - `BulqQA <http://zoningservice.maps-contentops.amiefarm.com/zoningservice/info>`__

- Live Cava Zoning Service (Legacy) - `AD <http://live-cava-cpp-r2.flatns.net/zoningservice/info>`__

- `Zoning Module Repository <https://bitbucket.tomtomgroup.com/projects/PROD/repos/zoning-module/browse>`__
