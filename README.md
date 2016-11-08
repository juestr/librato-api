# librato-api

[![npm version](http://img.shields.io/npm/v/librato-api.svg)](https://npmjs.org/package/librato-api)
[![Build Status](https://travis-ci.org/emartech/librato-api.svg?branch=master)](https://travis-ci.org/emartech/librato-api)
[![Coverage Status](https://coveralls.io/repos/github/emartech/librato-api/badge.svg?branch=master)](https://coveralls.io/github/emartech/librato-api?branch=master)
[![Dependencies Status](https://david-dm.org/emartech/librato-api.svg)](https://david-dm.org/emartech/librato-api)

A Librato backend API client library and a simple CLI tool.

This package allows you to manage your Librato backend configuration,
but is not intended to submit stats. There are other packages doing that
in a better way.

It can also be used to query metrics time series values by supplying
start\_time, end\_time, and other optional parameters to #getMetric.

For a full description of the Librato API see the official
[Librato API](https://www.librato.com/docs/api/) documentation.

At the moment support for the following sections is implemented:
Authentication, Pagination, Metrics, Spaces, Charts, Alerts, Services, Sources.

Explicit support for the following sections is missing:
Annotations, API Tokens, Jobs, Snapshots and Measurements Beta.
This is easy to fix, pull requests are welcome.

## Examples
    // the package is a ready to use client,
    // using LIBRATO_USER and LIBRATO_TOKEN from the process environment
    const libratoApi = require('librato-api')

    // create a client with custom config
    const LibratoApi = require('librato-api').LibratoAPI
    const libratoApi = new LibratoAPI({ auth: { user: '...', pass: '...' }, logger: ... })

    // all methods return Promises
    libratoApi.getMetrics().then(console.log)

    // most methods support an options object which is passed to request-promise
    libratoApi.getMetrics({ qs: { offset: 200, limit: 100 } })

    // iterates over pagination
    libratoApi.getAllMetrics()

    // get a metric definition
    libratoApi.getMetric('router.bytes')

    // for named metric, retrieve time series data for given time frame
    libratoApi.getMetric('router.bytes', { qs: { start_time: date1, end_time: date2 }})

    // update metric definition
    libratoApi.putMetric('customers', { 'period': 3600 })

    // use custom space finder (getSpace requires id)
    libratoApi.findSpaceByName('myspace')

    // update chart definition in a space
    libratoApi.putChart(myspace.id, mychartId, mychart)

    // not everything is explicitly supported yet, but generic api requests are easy to do
    libratoApi.apiRequest(['annotation', 'backup'], { method: 'PUT', body: { ... } })

## CLI Tool

This package installs a CLI tool named "librato" into your global or package bin-dir.

You have to export LIBRATO_USER and LIBRATO_TOKEN for authentication to work.

    $ export LIBRATO_USER='...'
    $ export LIBRATO_TOKEN='...'
    $ librato help
    $ librato list-metrics
    $ ...

### Warning

This tool is quite new and still a bit rough regarding command line parsing,
integrated help, etc. To see what it's doing it may be helpful to set LOG_LEVEL to verbose or debug.

### Configuration Directory Support

Apart from functions which model single API calls, the tool can take a local directory
containing json or js files in a certain structure and apply the contained metrics and spaces to
a Librato account with the "update-from-dir" command. The repository contains an example directory
"example-config" which demonstrates all features.

There is some templating support to create serieses of similar metrics. The "show-config-dir"
command can be used to debug templating easily.
