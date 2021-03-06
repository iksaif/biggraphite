# How to configure BigGraphite

Settings are either set in Graphite/Carbon configuration (when embedded), via command line flags, or via environment variables.

## Generic

- ```BG_STORAGE_DIR```: Storage directory to use, currently mostly use to store the cache
- ```BG_DRIVER```: One of ```cassandra``` (for production) or ```memory``` (for debug)

## Cassandra Backend

This is the main backend for BigGraphite and the one that should be used in production.

- ```BG_CASSANDRA_KEYSPACE```: keyspace to use (default: ```biggraphite```)
- ```BG_CASSANDRA_CONTACT_POINTS```: Cassandra contact points (default: ```127.0.0.1```)
- ```BG_CASSANDRA_PORT```: port to use to contact Cassandra (default: ```9042```)
- ```BG_CASSANDRA_CONNECTIONS```: number of Cassandra workers to use (default: ```4```)
- ```BG_CASSANDRA_TIMEOUT```: default timeout for operations (default: ```None```)
- ```BG_CASSANDRA_META_WRITE_CONSISTENCY```: Metadata write consistency (default: ```ONE```)
- ```BG_CASSANDRA_META_READ_CONSISTENCY```: Metadata read consistency (default: ```ONE```)
- ```BG_CASSANDRA_META_SERIAL_CONSISTENCY```: Metadata serial consistency (default: ```LOCAL_SERIAL```)
- ```BG_CASSANDRA_META_BACKGROUND_CONSISTENCY```: Metadata background consistency (default: ```LOCAL_QUORUM```)
- ```BG_CASSANDRA_DATA_READ_CONSISTENCY```: data read consistency (default: ```ONE```)
- ```BG_CASSANDRA_META_WRITE_CONSISTENCY```: Data write consistency (default: ```ONE```)
- ```BG_CASSANDRA_REPLICA_ID```: Identifier of this replica (default: ```0```)

You can also fine-tune the schema of your tables directly as needed. If you
want to active read repairs, you'll also want to set:
`'unsafe_aggressive_sstable_expiration': 'true'`.

## Memory Backend

The in-memory backend can be used during development to make debuging easier.

## Cache

In order to improve performances a metric metadata cache is used. This cache
reduces the number of round-trips between graphite/carbon and cassandra.

- ```BG_CACHE```: cache driver to use (memory or disk).
- ```BG_CACHE_SIZE```: size of the cache.
- ```BG_CACHE_TTL```: TTL of items in the cache
- ```BG_CACHE_SYNC```: when using disk cache, should writes be synchronous or not.
- ```BG_SYNC_EVERY_N_WRITES```: controls the amount of sync calls we do to Cassandra.

## Logging

See:
* http://python-guide-pt-br.readthedocs.io/en/latest/writing/logging/
* https://docs.djangoproject.com/en/1.11/topics/logging/

If you want to enable BigGraphite logging, please put the following in local_settings.py

```python
LOGGING = {
  # [...]
  'loggers': {
    'biggraphite': {
      'handlers': ['warning_file', 'debug_file', 'file', 'console'],
      'level': 'INFO',
      'propagate': False,
    },
  }
}
```

If you want to do that outside of django, just add:
```python
import logging
from logging import config

config.dictConfig(LOGGING)
```
