# canid(1) -- the caching additional network information daemon

## SYNOPSIS

`canid` [-file <cachefile>] [-expiry <sec>] [-concurrency <n>] [-port <port>]

## DESCRIPTION

Canid provides a simple web service for caching and simplifying information
retrieved from other services (see [BACKENDS][]) about Internet hostnames and
IP addresses. It is designed for use when looking up many addresses and/

On launch, Canid begins serving on the specified port. It shuts down cleanly
on SIGINT (^C on the console).

## OPTIONS

  * `-file` <cachefile> (default: no backing store)
    Use the given JSON file as a backing store for the cache.
    Loads the cache from this file on startup, and saves it on termination.

  * `-expiry` <sec> (default: 86400, 1 day)
    Expire cache entries after <sec> seconds.

  * `-concurrency` <n> (default: 16)
    Allow at most <n> simultaneous pending requests per backend.

  * `-port` <port> (default: 8043)
    TCP port to listen on

## RESOURCES

Canid provides three resources via HTTP:

  * `/`
    
    The root resource is an HTML page providing a simple web 
    front-end to the service.

  * `/prefix.json?addr=`

    Look up information about the prefix associated with an address, and
    return it as a JSON object. This object presently contains a `Prefix` key
    with the routed prefix associated with the address, an `ASN` key with a
    BGP autonomous system number associated with the address, and a
    `CountryCode` key for an ISO 3166 country code associated with the
    address. 

  * `/address.json?name=`

    Look up an Internet hostname via DNS, and return the IPv4 and IPv6
    addresses associated with it as a JSON object. This object contains a
    `Name` key with the name looked up, and an `Addresses` key containing an
    array of IPv4 and/or IPv6 addresses as strings. Looking up an address for
    a name will cause prefix information for all addresses found to be cached,
    as well.

All JSON resources also contain a `Cached` key, the time at which the data
entry was put into the cache in
[RFC3339][https://datatracker.ietf.org/doc/RFC3339] format.

## BACKENDS

The `prefix.json` resource currently uses the Prefix Overview and Geolocation
API entry points from [RIPEstat][https://stat.ripe.net].

The `address.json` resource uses DNS, as provided by the Go standard library's
`net.LookupIP()` (i.e., the system resolver)

## AUTHOR

Brian Trammell <brian@trammell.ch>

## LICENSE

Copyright (c) 2016 - 2018 Brian Trammell

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.