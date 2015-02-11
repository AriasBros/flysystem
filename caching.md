---
layout: default
permalink: /caching/
title: Caching
---

# Caching

Filesystem I/O is slow, so Flysystem uses cached filesystem meta-data to boost performance. When your application needs to scale you can also choose to use a (shared) persistent caching solution for this.
Or enable a per request caching (recommended).

Flysystem caches anything but the file contents. This keeps the cache small enough to be benefitial and covers all the filesystem inspection operations.

The following examples demonstrate how you can setup persistent meta-data caching:

## Predis Caching Setup

~~~ php
use League\Flysystem\Filesystem;
use League\Flysystem\Adapter\Local as Adapter;
use League\Flysystem\Cached\Storage\Predis as Cache;

$filesystem = new Filesystem(new Adapter(__DIR__.'/path/to/root'), new Cache);

// Or supply a client
$client = new Predis\Client;
$filesystem = new Filesystem(new Adapter(__DIR__.'/path/to/root'), new Cache($client));
~~~

## Memcached Caching Setup

~~~ php
use League\Flysystem\Filesystem;
use League\Flysystem\Adapter\Local as Adapter;
use League\Flysystem\Cached\Storage\Memcached as Cache;

$memcached = new Memcached;
$memcached->addServer('localhost', 11211);

$filesystem = new Filesystem(new Adapter(__DIR__.'/path/to/root'), new Cache($memcached, 'storageKey', 300));
// Storage Key and expire time are optional
~~~

## Adapter Caching Setup

~~~ php
use Dropbox\Client;
use League\Flysystem\Filesystem;
use League\Flysystem\Adapter\Dropbox;
use League\Flysystem\Adapter\Local;
use League\Flysystem\Cached\Storage\Adapter;

$client = new Client('token', 'app');
$dropbox = new Dropbox($client, 'prefix');

$local = new Local('path');
$cache = new Adapter($local, 'file', 300);
// Expire time is optional

$filesystem = new Filesystem($dropbox, $cache);
~~~

## Stash Caching Setup

~~~ php
use Stash\Pool;
use League\Flysystem\Adapter\Local as Adapter;
use League\Flysystem\Cached\Storage\Stash as Cache;

$pool = new Pool();
// you can optionally pass a driver (recommended, default: in-memory driver)

$cache = new Cache($pool, 'storageKey', 300);
// Storage Key and expire time are optional

$adapter = new Adapter(__DIR__.'/path/to/root');

$filesystem = new Filesystem($adapter, $cache);
~~~

For list of drivers and configuration options check their [documentation](http://www.stashphp.com/Drivers.html).
