..

This work is licensed under a Creative Commons Attribution 3.0 Unported License.
http://creativecommons.org/licenses/by/3.0/legalcode

============================
 Expose recordsets endpoint
============================

https://blueprints.launchpad.net/designate/+spec/expose-recordsets-api


Problem description
===================

Currently the ``/recordsets`` endpoint is a sub-resource of ``/zones``. But there is a need
to list all the records for a tenant in a single API call, so that doing filtering across
all the records under a tenant will be much easier.


Proposed change
===============

Designate will have a new API endpoint ``/v2/recordsets``.

* A single recordset can be retrieved via a GET call to ``/v2/recordsets/{recordset_id}``.
* All recordsets across all the zones owned by a tenant can be listed via a GET call to
  ``/v2/recordsets``, response will be paginated in this case if necessary.
* Filtering on all recordsets under a tenant will be supported, for example
  ``/v2/recordsets?name=foo``.


API Changes
-----------

API changes will be mainly about exposing the new API endpoint in controllers.

**Example single recordset retrieval request:**

.. code-block:: http

  GET /v2/recordsets/f7b10e9b-0cae-4a91-b162-562bc6096648 HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

  {
    "description": "This is an example recordset.",
    "links": {
        "self": "https://127.0.0.1:9001/v2/recordsets/f7b10e9b-0cae-4a91-b162-562bc6096648"
    },
    "updated_at": null,
    "records": [
        "10.1.0.2"
    ],
    "ttl": 3600,
    "id": "f7b10e9b-0cae-4a91-b162-562bc6096648",
    "name": "example.org.",
    "zone_id": "2150b1bf-dee2-4221-9d85-11f7886fb15f",
    "created_at": "2014-10-24T19:59:44.000000",
    "version": 1,
    "type": "A"
  }

**Example all recordset of a tenant retrieval request:**

.. code-block:: http

  GET /v2/recordsets HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

  {
    "recordsets": [
        {
            "description": null,
            "links": {
                "self": "https://127.0.0.1:9001/v2/recordsets/65ee6b49-bb4c-4e52-9799-31330c94161f"
            },
            "updated_at": null,
            "records": [
                "ns2.example.com."
            ],
            "ttl": null,
            "id": "65ee6b49-bb4c-4e52-9799-31330c94161f",
            "name": "example.org.",
            "zone_id": "2150b1bf-dee2-4221-9d85-11f7886fb15f",
            "created_at": "2014-10-24T19:59:11.000000",
            "version": 1,
            "type": "NS"
        },
        {
            "description": null,
            "links": {
                "self": "https://127.0.0.1:9001/v2/recordsets/14500cf9-bdff-48f6-b06b-5fc7491ffd9e"
            },
            "updated_at": "2014-10-24T19:59:46.000000",
            "records": [
                "ns2.example.com. joe.example.org. 1414180785 3600 600 86400 3600"
            ],
            "ttl": null,
            "id": "14500cf9-bdff-48f6-b06b-5fc7491ffd9e",
            "name": "example.org.",
            "zone_id": "2150b1bf-dee2-4221-9d85-11f7886fb15f",
            "created_at": "2014-10-24T19:59:12.000000",
            "version": 1,
            "type": "SOA"
        },
        {
            "description": null,
            "links": {
                "self": "https://127.0.0.1:9001/v2/recordsets/6c1584c8-c860-4bf7-93a2-a74935c3ddbe"
            },
            "updated_at": null,
            "records": [
                "ns2.example.com."
            ],
            "ttl": null,
            "id": "6c1584c8-c860-4bf7-93a2-a74935c3ddbe",
            "name": "example.org.",
            "zone_id": "11ae57ad-6a25-4d47-92ce-b0b9dfa40f15",
            "created_at": "2014-10-24T19:59:11.000000",
            "version": 1,
            "type": "NS"
        },
        {
            "description": null,
            "links": {
                "self": "https://127.0.0.1:9001/v2/recordsets/8a97568f-98c2-412f-9106-2bd672ce13d9"
            },
            "updated_at": "2014-10-24T19:59:46.000000",
            "records": [
                "ns2.example.com. joe.example.org. 1414180785 3600 600 86400 3600"
            ],
            "ttl": null,
            "id": "8a97568f-98c2-412f-9106-2bd672ce13d9",
            "name": "example.org.",
            "zone_id": "11ae57ad-6a25-4d47-92ce-b0b9dfa40f15",
            "created_at": "2014-10-24T19:59:12.000000",
            "version": 1,
            "type": "SOA"
        },
    ],
    "links": {
        "self": "https://127.0.0.1:9001/v2/recordsets"
    }
  }

**Example recordset filtering request:**

.. code-block:: http

  GET /v2/recordsets?data=1.2.3.* HTTP/1.1
  Host: 127.0.0.1:9001
  Accept: application/json
  Content-Type: application/json

  {
    "recordsets": [
        {
            "description": null,
            "links": {
                "self": "http://127.0.0.1:9001/v2/recordsets/077ce2b4-1d52-4c3f-8c70-07dfddeed5cc"
            },
            "updated_at": null,
            "records": [
                "1.2.3.4"
            ],
            "ttl": null,
            "id": "077ce2b4-1d52-4c3f-8c70-07dfddeed5cc",
            "name": "dns2.example.com.",
            "zone_id": "a4e29ed3-d7a4-4e4d-945d-ce64678d3b94",
            "created_at": "2014-07-08T20:28:26.000000",
            "priority": [
                null
            ],
            "version": 1,
            "type": "A"
        },
        {
            "description": null,
            "links": {
                "self": "http://127.0.0.1:9001/v2/recordsets/758004fe-4f0f-4756-a8f3-ee01ca5db8a2"
            },
            "updated_at": null,
            "records": [
                "1.2.3.99"
            ],
            "ttl": null,
            "id": "758004fe-4f0f-4756-a8f3-ee01ca5db8a2",
            "name": "mail2.example.com.",
            "zone_id": "1c7e5f64-4465-44f9-ba17-f9e819b5adef",
            "created_at": "2014-07-08T20:28:20.000000",
            "priority": [
                null
            ],
            "version": 1,
            "type": "A"
        }
    ],
    "links": {
        "self": "http://127.0.0.1:9001/v2/recordsets?data=1.2.3.*"
    }
  }

Central Changes
---------------

Central changes will include adding/changing functions for finding recordsets from storage
in central.service to support corresponding calls from api layer.

Storage Changes
---------------
None

Other Changes
-------------
None

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
None

Work Items
----------

* Make code changes to api and central
* Add unit and functional tests.

Dependencies
============
None
