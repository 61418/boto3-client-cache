.. _quickstart:

Quickstart
==========

The following examples demonstrate how to use the client cache with both LRU and LFU eviction policies, as well as how to handle and anticipate common exceptions.

LRU
---

Initialize an LRU client cache, set a client in the cache, and retrieve it using the same key.

.. code-block:: python

    from boto3_client_cache import ClientCache, ClientCacheKey
    import boto3

    # LRU is the default
    cache = ClientCache(max_size=30)
    kwargs = {"service_name": "s3", "region_name": "us-west-2"}
    key = ClientCacheKey(**kwargs)
    cache[key] = boto3.client(**kwargs)
    s3_client = cache[key]

LFU
---

Same as above but with an LFU client cache.

.. code-block:: python

    from boto3_client_cache import ClientCache, ClientCacheKey
    import boto3

    # LFU is not the default and must be specified
    cache = ClientCache("LFU", max_size=30)
    kwargs = {"service_name": "s3", "region_name": "us-west-2"}
    key = ClientCacheKey(**kwargs)
    cache[key] = boto3.client(**kwargs)
    s3_client = cache[key]

Error Semantics
---------------

.. code-block:: python

    # raises ClientCacheExistsError b/c client(**kwargs) already exists
    cache[key] = boto3.client(**kwargs)

    # raises ClientCacheNotFoundError b/c the specific client was not cached
    cache[ClientCacheKey(service_name="ec2", region_name="us-west-2")]

    # returns None instead of raising ClientCacheNotFoundError
    cache.get(ClientCacheKey(service_name="ec2", region_name="us-west-2"))

    # raises ClientCacheError b/c the key is not a ClientCacheKey
    cache["this is not a ClientCacheKey"]

    # raises ClientCacheError b/c the object is not a client
    cache[ClientCacheKey("s3")] = "this is not a boto3 client"