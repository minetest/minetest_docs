# HTTP API

Enables mods to make HTTP requests.

## Settings

To use this API, mods need to be listed in one or the other following 
settings in CSV format:
* `secure.trusted_mods`
* `secure.http_mods`

An example would be:

```
secure.http_mods = modname1, modnametwo, modenamethree
```

## Types

### HttpRequest

A table consisting of the following paramaters:

* url (required): 
  * `https://subdomain.domain.tld`
* timeout (optional): 
  * Time in seconds
  * Default is what `curl_timeout` setting is set to (NOTE: setting is 
    in milliseconds)
* method (optional):
  * Supports `GET`, `POST`, `PUT`, `DELETE`
  * Default is `GET`
* data (optional):
  * Supports string or table
  * Tables are encoded as `x-www-form-urlencoded` key value pairs
* post_data (optional):
  * Deprecated, see data above
* user_agent (optional):
  * Replaces the default user agent if provided
* extra_headers (optional):
  * Table of strings
  * Each string needs to follow http spec, `"key: value"`
* multipart (optional):
  * Boolean, default false
  * Method must be `POST`
  * Learn more at [MIME (Wikpedia)](https://en.wikipedia.org/wiki/MIME#Multipart_messages)

### HttpRequestResult

A table consisting of the following paramaters

* completed:
  * Boolean
  * If requested finished
* succeeded:
  * Boolean
  * Request status
* timeout:
  * If it timed out or not, see HttpRequest timeout
* code:
  * Int
  * Http status code
* data:
  * Table or string
  * Response returned

## Functions

### core.request_http_api()

* Returns a table containing the following functions provided:
  * That the mod in question has been placed in on of the two 
    settings above
  * The engine was built with curl support
  * Called from the mods init.lua file and not in a function

> [!WARNING]  
> Store the result in a local variable, not a global, so that 
> other mods cannot access it. Other mods should request their own 
> access

An example of how to use this api (handling both existence and permission 
being granted) is as follows:
```lua
    --inside mods init.lua file
    local http = core.request_http_api and core.request_http_api()

    if http then
        --call your http methods here
    end
```

### HttpApi.fetch(request, callback)

Calls callback once completed

* Request is of type HttpRequest
* Callback recieves one param of type HttpRequestResult

### HttpApi.fetch_async(request)

Returns a handle to be passed to `HttpApi.fetch_async_get`

* Request is of type HttpRequest

### HttpApi.fetch_async_get(handle)

Handle is provided by `HttpApi.fetch_async`