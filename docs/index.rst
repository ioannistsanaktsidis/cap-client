.. cap-client documentation master file, created by
sphinx-quickstart on Fri Aug 25 14:52:59 2017.
You can adapt this file completely to your liking, but it should at least
contain the root `toctree` directive.

Welcome to cap-client's documentation!
======================================

Installation
======

To install cap-client you just need to do the following.

.. code-block:: console

	$ pip install cap-client


Usage
=====

This guide assumes that you have successfully installed cap-client package already. If not, please follow the  :doc:`installation` instructions first.

To use cap-client you need a running CAP server, you can use the `CERN Production server <https://analysispreservation.cern.ch/>`_.

Next step is to generate an access token through the CAP server. For CERN Production server this can be done by following this `link <https://analysispreservation.cern.ch/profile/applications>`_.

Finally you need to set the required environment variables for the cap-client.

.. code-block:: console

	$ export CAP_SERVER_URL=https://analysispreservation.cern.ch/
	$ export CAP_ACCESS_TOKEN=<your generated access token from server>

Note that CAP_ACCESS_TOKEN can also be passed as an argument in the command line interface.

.. _types:

Retrieve current user information
----------

.. code-block:: console

	$ cap-client me

	{
        "collaborations":
            [
                "ATLAS",
                "LHCb",
                "CMS",
                "ALICE"
            ],
        "id": 1,
        "email": "user@cern.ch"
    }


Get all types of analyses available
----------

.. code-block:: console

	$ cap-client types

	Available types:
		atlas-workflows
		alice-analysis
		atlas-analysis
		lhcb
		cms-questionnaire
		cms-analysis


Get analysis with given PID
----------

You can get existing analyses details only if you have at least read access to it.

You need to specify 
 
	--pid  the PID of an analysis.

.. code-block:: console

	$ cap-client get --pid/-p <existing pid>

	E.g $ cap-client get --pid 883090d3c1784aeabe9e23412a81239e

	{   
		"pid": "883090d3c1784aeabe9e23412a81239e",
	    "basic_info": {
	        "abstract": "Example abstract",
	        "people_info": [
	            {
	                "name": "John doe"
	            },
	            {
	                "name": "J doe"
	            }
	        ],
	        "analysis_number": "test"
	    }
	}


Create analysis
----------

You can create a new analysis by specifing

	--file  a file with the json data corresponding to the analysis JSON Schema.
	--type  the type of analysis you want to create.

If you don't know what is the type of analyses you want to create use this 
:ref:`link <types>`.

You can only choose to create the type of analysis that you are affiliated.
E.g if you are a CMS member you can only create analysis with type cms-analysis or 
cms-questionnaire.


.. code-block:: console

	$ cap-client create --file/-f <file with JSON data>  --type/-t <type of analysis> 

	E.g $ cap-client create --file test.json --type cms-analysis

	{
		'status': 200, 
		'data': {   
			"pid": "883090d3c1784aeabe9e23412a81239e",
		    "basic_info": {
		        "abstract": "Example abstract",
		        "people_info": [
		            {
		                "name": "John doe"
		            },
		            {
		                "name": "J doe"
		            }
		        ],
		        "analysis_number": "test"
		    }
		}
	}
		


Delete analysis
----------

You can delete an existing analysis by specifing

	--pid  the PID as a parameter.

	.. code-block:: console

		$ cap-client delete --pid/-p <existing pid>

		E.g $ cap-client delete --pid 4c734c3ae5b14a2195e3b17dc9ff63ae

		Server response:
			{
				'status': 204, 
			 	'data': None
			}


Update analysis
----------

You can update an existing analysis by specifing

	--pid  the PID as a parameter.
	--file  a file with the json data corresponding to the analysis JSON Schema.


	.. code-block:: console

		$ cap-client update --pid/-p <existing pid> --file/-f <file with JSON data>

		E.g $ cap-client update --pid 883090d3c1784aeabe9e23412a81239e --file test.json

		{
		 	'status': 200, 
		 	'data': {   
				"pid": "883090d3c1784aeabe9e23412a81239e",
			    "basic_info": {
			        "abstract": "Example abstract",
			        "people_info": [
			            {
			                "name": "John doe"
			            },
			            {
			                "name": "J doe"
			            }
			        ],
			        "analysis_number": "test"
			    }
			}
		}

Patch analysis
----------

You can patch an existing analysis by specifing

	--pid  the PID as a parameter.
	--file  a file with the changes in `JSON patch format <http://jsonpatch.com/>`_.

Example changes in JSON patch format:

	.. code-block:: javascript
		[ { "op": "add", "path": "/basic_info/analysis_number", "value": "22" }]

.. code-block:: console

	$ cap-client patch --pid/-p <existing pid> --file/-f <file with JSON data>

	E.g $ cap-client patch --pid 883090d3c1784aeabe9e23412a81239e --file test.json
	
	{
	 	'status': 200, 
	 	'data': {   
			"pid": "883090d3c1784aeabe9e23412a81239e",
		    "basic_info": {
		        "abstract": "Example abstract",
		        "people_info": [
		            {
		                "name": "John doe"
		            },
		            {
		                "name": "J doe"
		            }
		        ],
		        "analysis_number": "test"
		    }
		}
	}


Metadata
========

Get Metadata
--------

You can get existing analyses metadata only if you have at least read access to it.

You need to specify

	--pid  the PID of an analysis.

.. code-block:: console

    $ cap-client metadata get <field> --pid/-p <existing pid>

    E.g $ cap-client metadata get basic_info.description --pid 4b2924db6c32467bb2de6221f4faf167

    "Very Interesting Description"


Edit Metadata
-------------

You can edit and change existing metadata details if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis.


.. code-block:: console

    $ cap-client metadata set <field> <new value> --pid/p <existing pid>

    E.g $ cap-client metadata set basic_info.description "Very Interesting Description" --pid 4b2924db6c32467bb2de6221f4faf167

    {
        "$ana_type": "lhcb",
        "$schema": "https://macbook-trzcinska.cern.ch:5000/schemas/deposits/records/lhcb-v0.0.1.json",
            "basic_info": {
                "description": "Very Interesting Description"
            }
    }

    $ cap-client --verbose metadata append basic_info.my_array "New element" --pid 0af85220ef0c492889658539d8b3d4e2

    {
        "$ana_type": "lhcb",
        "$schema": "https://macbook-trzcinska.cern.ch:5000/schemas/deposits/records/lhcb-v0.0.1.json",
        "basic_info": {
            "my_array": [
                "New element"
            ],
            "description": "Very Interesting Description"
        }
    }


Permissions
===========


Get permissions
-----------

You can get existing analyses user permissions only if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis.

.. code-block:: console

    $ cap-client permissions get --pid/p <existing pid>

    {
        "updated": "2018-02-12T15:57:31.824619+00:00",
        "metadata": {
            "deposit-admin": {
                "user": [],
                "roles": []
            },
            "deposit-update": {
                "user": [],
                "roles": []
            },
            "deposit-read": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
                }
            },
        "created": "2018-02-12T15:15:40.697516+00:00"
    }


Set permissions
-----------

You can set existing analyses user permissions only if you have at least read access to it.

You need to specify

    --rights  the permission rights. You can choose between read, update and admin.
    --user  the email of the user to grant permissions.
    --pid  the PID of an analysis you want to set permissions.

.. code-block:: console

    $ cap-client permissions add --rights/-r [read | update | admin] --user/-u <email> --pid/p <existing pid>

    E.g $ cap-client permissions add -r update -u alice@inveniosoftware.org -p 0af85220ef0c492889658539d8b3d4e2

    {
        "updated": "2018-02-12T15:57:31.824619+00:00",
        "metadata": {
            "deposit-admin": {
                "user": [],
                "roles": []
            },
            "deposit-update": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
            },
            "deposit-read": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
                }
            },
        "created": "2018-02-12T15:15:40.697516+00:00"
    }

Remove permissions
-----------

You can remove existing analyses user permissions only if you have at least read access to it.

You need to specify

    --rights  the permission rights. You can choose between read, update and admin.
    --user  the email of the user to grant permissions.
    --pid  the PID of an analysis you want to set permissions.

.. code-block:: console

    $ cap-client permissions remove --rights/-r [read | update | admin] --user/-u <email> --pid/p <existing pid>

    E.g $ cap-client permissions remove -r update -u alice@inveniosoftware.org -p 0af85220ef0c492889658539d8b3d4e2

    {
        "updated": "2018-02-12T15:57:31.824619+00:00",
        "metadata": {
            "deposit-admin": {
                "user": [],
                "roles": []
            },
            "deposit-update": {
                "user": [],
                "roles": []
            },
            "deposit-read": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
                }
            },
        "created": "2018-02-12T15:15:40.697516+00:00"
    }
