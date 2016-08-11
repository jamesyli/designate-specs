..

This work is licensed under a Creative Commons Attribution 3.0 Unported License.
http://creativecommons.org/licenses/by/3.0/legalcode

======================================
The design of /v2/reverse/fixedips API
======================================

https://blueprints.launchpad.net/designate/+spec/fixed-ip-ptrs


Problem description
===================

This spec proposes to add an API for PTR operations on the allocated fixed IP of cloud servers
and load balancers.


Proposed change
===============

1. CRUD operations on /v2/reverse/fixedips endpoint;
2. Optional IP validation via a pluggable way.


API Changes
-----------

API changes will be mainly about exposing the new API endpoint in controllers.

POST /v2/reverse/fixedips
^^^^^^^^^^^^^^^^^^^^^^^^^
Create a PTR record

**Example request:**

.. code-block:: http

  POST /v2/reverse/fixedips HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

  {
    "records" : [{
      "name" : "email.example.com",
      "type" : "PTR",
      "data" : "192.168.1.2",
      "ttl" : 6000
    }],
    "metadata" : {
      "region" : "region-1",
      "type" : "server",
      "instance": "db7eecc7-e1e3-4150-b9fb-2c80598b13e3"
    }
  }

The metadata section is for ip ownership validation, and it is optional.

**Example response:**

.. code-block:: http

  HTTP/1.1 202 Accepted
  Content-Type: application/json

  {
    "records" : [{
      "id" : "f7b10e9b-0cae-4a91-b162-562bc6096648",
      "name" : "email.example.com",
      "type" : "PTR",
      "data" : "192.168.1.2",
      "ttl" : 6000,
      "created_at" : "2016-08-01T20:09:48.094457",
      "description": null,
      "status": "PENDING",
      "project_id": "4335d1f0-f793-11e2-b778-0800200c9a66",
    }],
  }


PUT /v2/reverse/fixedips/{ID}
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Modify a PTR record

**Example request:**

.. code-block:: http

  PUT /v2/reverse/fixedips/f7b10e9b-0cae-4a91-b162-562bc6096648  HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

  {
    "name" : "newemail.example.com",
    "ttl" : 6600,
  }

**Example response:**

.. code-block:: http

  HTTP/1.1 202 Accepted
  Content-Type: application/json

  {
    "id" : "f7b10e9b-0cae-4a91-b162-562bc6096648",
    "name" : "newemail.example.com",
    "type" : "PTR",
    "data" : "192.168.1.2",
    "ttl" : 6600,
    "created_at" : "2016-08-01T20:09:48.094457",
    "updated_at" : "2016-08-02T20:09:49.094450",
    "description": null,
    "status": "PENDING",
    "project_id": "4335d1f0-f793-11e2-b778-0800200c9a66",
  }

GET /v2/reverse/fixedips
^^^^^^^^^^^^^^^^^^^^^^^^
List PTR records owned by a project

**Example request:**

.. code-block:: http

  GET /v2/reverse/fixedips  HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

**Example response:**

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "records": [
      {
        "name": "newemail.example.com",
        "id": "f7b10e9b-0cae-4a91-b162-562bc6096648",
        "type": "PTR",
        "data": "192.168.1.2",
        "ttl": 6600,
        "updated": "2016-08-05T19:41:40.000+0000",
        "created": "2016-08-03T22:21:19.000+0000",
        "description": null,
        "project_id": "4335d1f0-f793-11e2-b778-0800200c9a66",
        "status": "ACTIVE",
      },
      {
        "name": "email2.example.com",
        "id": "89acac79-38e7-497d-807c-a011e1310438",
        "type": "PTR",
        "data": "2001:4800:7812:514:be76:4eff:fe05:5bb8",
        "ttl": 900,
        "updated": "2016-08-05T19:48:21.000+0000",
        "created": "2016-08-05T19:48:21.000+0000",
        "description": null,
        "project_id": "4335d1f0-f793-11e2-b778-0800200c9a66",
        "status": "ACTIVE"
      }
    ],
  }

GET /v2/reverse/fixedips/{ID}
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Show a PTR record

**Example request:**

.. code-block:: http

  GET /v2/reverse/fixedips/89acac79-38e7-497d-807c-a011e1310438  HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

**Example response:**

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "name": "email2.example.com",
    "id": "89acac79-38e7-497d-807c-a011e1310438",
    "type": "PTR",
    "data": "2001:4800:7812:514:be76:4eff:fe05:5bb8",
    "ttl": 900,
    "updated": "2016-08-05T19:48:21.000+0000",
    "created": "2016-08-05T19:48:21.000+0000",
    "description": null,
    "project_id": "4335d1f0-f793-11e2-b778-0800200c9a66",
    "status": "ACTIVE"
  }

DELETE /v2/reverse/fixedips/{ID}
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Delete a PTR record

**Example request:**

.. code-block:: http

  DELETE /v2/reverse/fixedips/89acac79-38e7-497d-807c-a011e1310438  HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

**Example response:**

.. code-block:: http

  HTTP/1.1 202 Accepted
  Content-Type: application/json

  {
    "name": "email2.example.com",
    "id": "89acac79-38e7-497d-807c-a011e1310438",
    "type": "PTR",
    "data": "2001:4800:7812:514:be76:4eff:fe05:5bb8",
    "ttl": 900,
    "updated": "2016-08-05T19:48:21.000+0000",
    "created": "2016-08-05T19:48:21.000+0000",
    "description": null,
    "project_id": "4335d1f0-f793-11e2-b778-0800200c9a66",
    "status": "PENDING"
  }


Central Changes
---------------
Central communicates with storage upon messages received from api, and returns requested
PTR objects to api.


Storage Changes
---------------
A new PTR table.

Other Changes
-------------
Any necessary changes to pool manager and miniDNS for backend persistence.

Alternatives
------------
None

Implementation
==============

Assignee(s)
-----------
None

Milestones
----------
newton-1

Work Items
----------

* Make code changes to api, central and storage
* Add unit and functional tests.

Dependencies
============
None
