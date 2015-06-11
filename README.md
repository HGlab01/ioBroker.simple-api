![Logo](admin/simple-api.png)
ioBroker simple-api adapter
=================
This is RESTFul interface to read the objects and states from ioBroker and to write/control the states over HTTP Get/Post requests.

## Changelog
### 0.1.0 (2015-06-10)
* (bluefox) change setForeignState api
* (bluefox) support of user permissions

### 0.0.4 (2015-03-11)
* (bluefox) remove socket.io from file

### 0.0.3 (2015-02-13)
* (bluefox) remove socket.io from dependencies

### 0.0.2 (2015-02-12)
* (bluefox) enable be a part of "web"

### 0.0.1 (2015-02-06)
* (bluefox) initial commit

## Install

```node iobroker.js add simple-api```

## Usage
Assume, we have no security and the server runs on default port 8087.

For all queries the name or id of the state can be specified.

For every requiest that returns JSON you can set parameter *prettyPrint* to get the output in human readable form.

If authentication is enabled, two other fields are mandatory: <pre>?user=admin&pass=iobroker</pre>

### getPlainValue 
Read state value as text. You can specify more ids divided by semicolon

<pre>http://ip:8087/getPlainValue/admin.0.memHeapTotal</pre>

<pre>   
  31.19
</pre>  

<pre>http://ip:8087/getPlainValue/admin.0.memHeapTotal, admin.0.memHeapUsed</pre>
<pre>  
  31.19
  17.52
</pre>

### get 
Read state and object data of state as json. You can specify more ids divided by semicolon.
If more than one ID requested, the JSON array will be returned.
  
<pre>http://localhost:8087/get/admin.0.memHeapTotal/?prettyPrint</pre>
  
<pre>
  {
    "val": 31.19,
    "ack": true,
    "ts": 1423154619,
    "from": "system.adapter.admin.0",
    "lc": 1423153989,
    "_id": "system.adapter.admin.0.memHeapTotal",
    "type": "state",
    "common": {
      "name": "admin.0.memHeapTotal",
      "type": "number",
      "role": "indicator.state",
      "unit": "MB",
      "history": {
        "enabled": true,
        "changesOnly": true,
        "minLength": 480,
        "maxLength": 960,
        "retention": 604800,
        "debounce": 10000
      }
    },
    "native": {}
  }
</pre>
  
<pre>http://ip:8087/get/admin.0.memHeapTotal,admin.0.memHeapUsed/?prettyPrint</pre>
<pre>
  [
    {
      "val": 31.19,
      "ack": true,
      "ts": 1423154544,
      "from": "system.adapter.admin.0",
      "lc": 1423153989,
      "_id": "system.adapter.admin.0.memHeapTotal",
      "type": "state",
      "common": {
        "name": "admin.0.memHeapTotal",
        "type": "number",
        "role": "indicator.state",
        "unit": "MB",
        "history": {
          "enabled": true,
          "changesOnly": true,
          "minLength": 480,
          "maxLength": 960,
          "retention": 604800,
          "debounce": 10000
        }
      },
      "native": {}
    },
    {
      "val": 16.25,
      "ack": true,
      "ts": 1423154544,
      "from": "system.adapter.admin.0",
      "lc": 1423154544,
      "_id": "system.adapter.admin.0.memHeapUsed",
      "type": "state",
      "common": {
        "name": "admin.0.memHeapUsed",
        "type": "number",
        "role": "indicator.state",
        "unit": "MB",
        "history": {
          "enabled": true,
          "changesOnly": true,
          "minLength": 480,
          "maxLength": 960,
          "retention": 604800,
          "debounce": 10000
        }
      },
      "native": {}
    }
  ]
</pre>
  
### getBulk 
Read the states of more IDs with timestamp. You can specify more ids divided by semicolon.
Always the JSON array will be returned.
  
<pre>http://ip:8087/getBulk/admin.0.memHeapTotal,admin.0.memHeapUsed/?prettyPrint</pre>
  
<pre>
  [
    {
      "val": 31.19,
      "ts": 1423154754
    },
    {
      "val": 15.6,
      "ts": 1423154754
    }
  ]
</pre>
  
### set 
Write the states with specified IDs. You can specifiy *wait* option in milliseconds to wait for answer from driver.
 
<pre>http://ip:8087/set/hm-rpc.0.IEQ12345.LEVEL?value=1&prettyPrint</pre>
<pre>{
       "id": "hm-rpc.0.IEQ12345.LEVEL",
       "value": 1
     }
</pre>
  
<pre>http://ip:8087/set/hm-rpc.0.IEQ12345.LEVEL?value=1&wait=5000&prettyPrint</pre>
<pre>{
       "val": 1,
       "ack": true,
       "ts": 1423155399,
       "from": "hm-rpc.0.IEQ12345.LEVEL",
       "lc": 1423155399
     }
</pre>

If no answer will be recieved in specified time, the *null* value will be returned. 
In the first case the answer will be returned immediately and *ack* is false. In the second case *ack* is true. That means it was response from driver.
   
### setBulk 
- write bulk of IDs in one request.

<pre>http://ip:8087/setBulk?hm-rpc.0.FEQ1234567:1.LEVEL=0.7&Anwesenheit=0&prettyPrint</pre>
<pre>
  [
    {
      "id": "hm-rpc.0.FEQ1234567:1.LEVEL",
      "val": "0.7"
    },
    {
      "error": "error: datapoint \"Anwesenheit\" not found"
    }
  ]
</pre>
You can send this request as POST too.

### objects 
Get the list of all objects for pattern. If no pattern specified  all objects as JSON array will be returned.

<pre>http://ip:8087/objects?prettyPrint</pre>
<pre>
  {
  "system.adapter.admin.0.uptime": {
    "_id": "system.adapter.admin.0.uptime",
    "type": "state",
    "common": {
      "name": "admin.0.uptime",
      "type": "number",
      "role": "indicator.state",
      "unit": "seconds"
    },
    "native": {}
  },
  "system.adapter.admin.0.memRss": {
    "_id": "system.adapter.admin.0.memRss",
    "type": "state",
    "common": {
      "name": "admin.0.memRss",
      "desc": "Resident set size",
      "type": "number",
      "role": "indicator.state",
      "unit": "MB",
      "history": {
        "enabled": true,
        "changesOnly": true,
        "minLength": 480,
        "maxLength": 960,
        "retention": 604800,
        "debounce": 10000
      }
    },
    "native": {}
  },
  ...
</pre>

  Get all control objects of adapter system.adapter.admin.0: 
<pre>http://ip:8087/objects?pattern=system.adapter.admin.0*&prettyPrint</pre>
<pre>
    {
    "system.adapter.admin.0.uptime": {
      "_id": "system.adapter.admin.0.uptime",
      "type": "state",
      "common": {
        "name": "admin.0.uptime",
        "type": "number",
        "role": "indicator.state",
        "unit": "seconds"
      },
      "native": {}
    },
    ...
    
</pre>
    
### states 
Get the list of all states for pattern. If no pattern specified all states as JSON array will be returned.

<pre>http://ip:8087/states?prettyPrint</pre>
<pre>
  {
    "system.adapter.admin.0.uptime": {
      "val": 32176,
      "ack": true,
      "ts": 1423156164,
      "from": "system.adapter.admin.0",
      "lc": 1423156164
    },
    "system.adapter.admin.0.memRss": {
      "val": 41.14,
      "ack": true,
      "ts": 1423156164,
      "from": "system.adapter.admin.0",
      "lc": 1423156119
    },
    "system.adapter.admin.0.memHeapTotal": {
      "val": 31.19,
      "ack": true,
      "ts": 1423156164,
      "from": "system.adapter.admin.0",
      "lc": 1423155084
    },
  ...
</pre>

  Get all control objects of adapter system.adapter.admin.0: 
<pre>http://ip:8087/states?pattern=system.adapter.admin.0*&prettyPrint</pre>
<pre>
    {
      "system.adapter.admin.0.uptime": {
        "val": 32161,
        "ack": true,
        "ts": 1423156149,
        "from": "system.adapter.admin.0",
        "lc": 1423156149
      },
      "system.adapter.admin.0.memRss": {
        "val": 41.14,
        "ack": true,
        "ts": 1423156149,
        "from": "system.adapter.admin.0",
        "lc": 1423156119
      },
      "system.adapter.admin.0.memHeapTotal": {
        "val": 31.19,
        "ack": true,
        "ts": 1423156149,
        "from": "system.adapter.admin.0",
        "lc": 1423155084
      },
      "system.adapter.admin.0.memHeapUsed": {
        "val": 19.07,
        "ack": true,
        "ts": 1423156149,
        "from": "system.adapter.admin.0",
        "lc": 1423156149
      },
      "system.adapter.admin.0.connected": {
        "val": true,
        "ack": true,
        "ts": 1423156149,
        "from": "system.adapter.admin.0",
        "lc": 1423128324,
        "expire": 28100
      },
      "system.adapter.admin.0.alive": {
        "val": true,
        "ack": true,
        "ts": 1423156149,
        "from": "system.adapter.admin.0",
        "lc": 1423128324,
        "expire": 28115
      }
    }
</pre>