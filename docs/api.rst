.. _api:

API
===

The primary interface for boto3-client-cache is :class:`boto3_client_cache.cache.ClientCache`,
which can be used to cache and retrieve boto3 clients efficiently. The cache is designed to be 
thread-safe and supports LRU and LFU eviction policies.

:class:`boto3_client_cache.cache.ClientCache` can be used exactly like a standard Python 
dictionary, with the exception of the ``fromkeys``, ``update``, and ``setdefault`` methods, as 
well as the ``|=`` and ``|`` operator.

.. important::

   To interact with the cache, you **must** use the :class:`boto3_client_cache.cache.ClientCacheKey` 
   object to create unique keys and fetch clients. Additionally, assignments **must** be boto3 client objects.

Example
-------

.. code-block:: python

    from boto3_client_cache import ClientCache, ClientCacheKey
    import boto3

    # create an LRU client cache with a maximum size of 30
    cache = ClientCache(max_size=30)

    # store boto3 client params in an object
    kwargs = {"service_name": "s3", "region_name": "us-west-2"}

    # create a cache key using those params
    key = ClientCacheKey(**kwargs)

    # make the assignment
    cache[key] = boto3.client(**kwargs)

    # and retrieve the client using the key
    s3_client = cache[key]

    # this raises a ClientCacheExistsError
    cache[key] = boto3.client(**kwargs)

    # this raises a ClientCacheNotFoundError
    cache[ClientCacheKey(service_name="ec2", region_name="us-west-2")]

    # but this returns None instead of raising ClientCacheNotFoundError
    cache.get(ClientCacheKey(service_name="ec2", region_name="us-west-2"))

    # this raises a ClientCacheError
    cache["this is not a ClientCacheKey"]

    # and this raises a ClientCacheError
    cache[ClientCacheKey("s3")] = "this is not a boto3 client"

Modules
-------

Refer to the following modules for more details on configuration, implementation, and 
available exceptions.

.. autosummary::
   :toctree: reference
   :recursive:

   boto3_client_cache.cache
   boto3_client_cache.exceptions
