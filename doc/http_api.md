# HTTP API

Minetest's HTTP API can be used to fetch data via http from endpoints

## Settings

To use this api, mods need to be listed in one or the other following 
settings in csv format:
* `secure.trusted_mods`
* `secure.http_mods`

an example would be:

```
secure.http_mods = modname1, modnametwo, modenamethree
```

## Types

### HttpRequest

A table consisting of the following paramaters

* url (required): 
  * `http://subdomain.domain.tld`
* timeout (optional): 
  * time in seconds
  * default is what `curl_timeout` setting is set to (NOTE: setting is 
    in milliseconds)
* method (optional):
  * supports `GET`, `POST`, `PUT`, `DELETE`
  * default is `GET`
* data (optional):
  * supports string or table
  * tables are encoded as `x-www-form-urlencoded` key value pairs
* post_data (optional):
  * deprecated, see data above
* user_agent (optional):
  * replaces the default user agent if provided
* extra_headers (optional):
  * table of strings
  * each string needs to follow http spec, `"key: value"`
* multipart (optional):
  * boolean, default false
  * method must be `POST`
  * [see here for more info](https://en.wikipedia.org/wiki/MIME#Multipart_messages)

### HttpRequestResult

A table consisting of the following paramaters

* completed:
  * boolean
  * if requested finished
* succeeded:
  * boolean
  * request status
* timeout:
  * if it timed out or not, see HttpRequest timeout
* code:
  * int
  * http status code
* data:
  * table or string
  * response returned

## Functions

### core.request_http_api()

* returns a table containing the following functions provided:
  * that the mod in question has been placed in on of the two 
    settings above
  * the engine was built with curl support
  * called from the mods init.lua file and not in a function

> [!WARNING]  
> Store the result in a local variable, not a global so that 
> other mods cannot access it, they should request their own 
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

### httpapi.fetch(request, callback)

calls callback once completed

* request is of type HttpRequest
* callback recieves one param of type HttpRequestResult

### HttpApi.fetch_async(request)

returns a handle to be passed to `HttpApi.fetch_async_get`

* request is of type HttpRequest

### HttpApi.fetch_async_get(handle)

handle is provided by `HttpApi.fetch_async`