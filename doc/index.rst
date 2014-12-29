jsonrpcserver
=============

Receive `JSON-RPC <http://www.jsonrpc.org/>`_ requests in a `Flask
<http://flask.pocoo.org/>`_ app.

The library has two features:

#. A dispatcher, which validates incoming requests and then passes them on to
   your own code to carry out the request.

#. A `Flask blueprint <http://flask.pocoo.org/docs/0.10/blueprints/>`_ to catch
   errors, ensuring we always respond with JSON-RPC.

Installation
------------

.. code-block:: sh

    $ pip install jsonrpcserver

Usage
-----

Create a Flask app and register the blueprint.

.. code-block:: python

    from flask import Flask
    from jsonrpcserver import bp, dispatch, exceptions

    app = Flask(__name__)
    app.register_blueprint(bp)

Add a route to dispatch requests to the handling methods.

.. code-block:: python

    @app.route('/', methods=['POST'])
    def index():
        return dispatch(HandleRequests)

Now go ahead and write the methods that will carry out the requests.

.. code-block:: python

    class HandleRequests:
        def add(x, y):
            return x + y

Keyword arguments are also allowed.

.. code-block:: python

    def find(name='Foo', age=42, **kwargs):

.. important::

    Use either positional or keyword arguments, but not both in the same
    method. See `Parameter Structures
    <http://www.jsonrpc.org/specification#parameter_structures>`_ in the
    specs.

Exceptions
^^^^^^^^^^

When arguments are invalid, raise ``InvalidParams``.

.. code-block:: python

    def add(x, y):
        try:
            return x + y
        except TypeError as e:
            raise exceptions.InvalidParams('Type error')

The blueprint will catch the exception and ensure this is returned::

    {"jsonrpc": "2.0", "error": {"code": -32602, "message": "Invalid params", "data": "Type error"}, "id": 1}

See it all put together here:
https://bitbucket.org/beau-barker/jsonrpcserver/src/tip/run.py

Logging
-------

To see the underlying messages going back and forth, set the logging level
to INFO.

.. code-block:: python

    import logging
    logging.getLogger('jsonrpcserver').setLevel(logging.INFO)

Todo
----

* Support `batch calls <http://www.jsonrpc.org/specification#batch>`_.

Links
-----

* Package: https://pypi.python.org/pypi/jsonrpcserver
* Repository: https://bitbucket.org/beau-barker/jsonrpcserver
* Issue tracker: https://bitbucket.org/beau-barker/jsonrpcserver/issues

If you need a client, try my `jsonrpcclient
<http://jsonrpcclient.readthedocs.org/>`_ library.