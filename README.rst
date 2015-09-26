seamless
========

ssh authentication for web services


Setting up a seamless user
---------------------------

First, you have to create a user for seamless, and add keys that are allowed to get tokens via seamless (on ``seamless-host``):

.. code:: bash

    $ sudo seamless init ${NAME}
    $ sudo seamless add ${NAME} /path/to/public/key --user ${USER}

A user with this public key is now able to get tokens via ssh:

.. code::

    $ ssh ${NAME}@seamless-host -

or from Python

.. code:: python

    import seamless
    
    seamless.get_token('name@seamless-host')


Protecting a webservice with WSGI middleware
---------------------------------------------

seamless ships with WSGI middleware that verifies that a valid seamless token is passed via the Authorization header.

.. code:: python

    from seamless.wsgi import SeamlessMiddleware
    
    app = ...

    app = SeamlessMiddleware(app, max_age=60, secret='...')


Making requests to such a protected app is made easy with an auth plugin for `requests`_:

.. code:: python

    import requests
    from seamless.requests import SeamlessAuth
    
    session = requests.Session()
    session.auth = SeamlessAuth('name@seamless-host')

    session.get('http://protected-app/')


The token obtained from `seamless-host` is cached. 
It will be be automatically refreshed when it expires, and the failing request retried.


.. _requests: http://docs.python-requests.org/

