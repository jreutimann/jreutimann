---
marp: true
theme: theme
backgroundImage: url('img/bg.png')
author: Jan Reutmann
footer: RESTful APIs - March 2024
---

# RESTful APIs - a Refresher

<br>

![height:500px center](img/four_rules.jpeg)

---
# RESTful APIs - a Refresher

<br>

![height:500px center](img/BackendRestAPIFrontEndBike.jpeg)

---

<style scoped>
  li {
   font-size: 21px
  }
</style>

# RESTful APIs - a Refresher
## Agenda
<br>
<br>
<br>
<div class="columns"><div>

REST
  + Definition
  + Maturity Principle
  + REST vs HTTP
  + Principles
  + Cacheing
  + Encoding / Compression
  + HTTP Response Codes

</div><div>

Conventions
  + Naming
  + JSON
  + POLYPOINT best practises
  + Versioning
  + Long Running Tasks

Security
  + Considerations
  + OAuth
    - OIDC
    - JWT


</div></div>

---

# What is REST?
## REST | Definition

<br>
<br>

Presented by _Roy Fielding_  in 2000
<br>
<div class="box">

REST (**representational state transfer**) is a _software architectural style_ that was created to guide the design and development of the architecture for the World Wide Web

</div>
<br>

<div class="box">

A Web API (or Web Service) conforming to the REST architectural style is called a **RESTful API**.

</div>

---
# What is REST?
## REST | Richardson Maturity Model

<br>
<br>

![width:600px center](img/maturity.png)

---
# What is REST?
## REST | Richardson Maturity Model


![width:400px right](img/maturity.png)

### Level 0: The Swamp of POX (RPC over HTTP)

- one endpoint
- one verb (POST)
- payload determines request

<style scoped>
  pre {
    width: 60%;
    font-size: 16px;
  }
</style>
<br>

```
POST /appointmentService

<appointmentRequest>
  <slot doctor = "mjones" start = "1400" end = "1450"/>
  <patient id = "jsmith"/>
</appointmentRequest>
```

---
# What is REST?
## REST | Richardson Maturity Model


![width:400px right](img/maturity.png)

### Level 1: Resources

-  address Resources with URI

<style scoped>
  pre {
    width: 60%;
    font-size: 16px;
  }
</style>
<br>

```
POST /doctors/mjones
POST /slots/1234
...
```

---
# What is REST?
## REST | Richardson Maturity Model

![width:400px right](img/maturity.png)

### Level 2: HTTP verbs & status codes

-  use proper HTTP verbs
-  user correct HTTP status code

<style scoped>
  pre {
    width: 60%;
    font-size: 16px;
  }
</style>
<br>

```
POST /slots/1234

HTTP/1.1 201 Created
Location: slots/1234/appointment
[various headers]
<appointment>
  <slot id = "1234" doctor = "mjones" start = "1400" end = "1450"/>
  <patient id = "jsmith"/>
</appointment>

```


---
# What is REST?
## REST | Richardson Maturity Model


![width:400px right](img/maturity.png)

### Level 3 - Hypermedia (HATEOAS)

<style scoped>
  pre {
    width: 60%;
    font-size: 16px;
  }
</style>
<br>

```
GET /doctors/mjones/slots?date=20100104&status=open

HTTP/1.1 200 OK
[various headers]
<openSlotList>
  <slot id = "1234" doctor = "mjones" start = "1400" end = "1450">
     <link rel = "/linkrels/slot/book" 
           uri = "/slots/1234"/>
  </slot>
  <slot id = "5678" doctor = "mjones" start = "1600" end = "1650">
     <link rel = "/linkrels/slot/book" 
           uri = "/slots/5678"/>
  </slot>
</openSlotList>
```
---
# What is REST?
## REST | Richardson Maturity Model

<br>

<div class="columns"><div>

![height:350px](img/batman.png)

</div><div>

<br><br><br>
<div class="box">
We aim for level 2!
</div>

</div>


---
# What is REST?
## REST vs HTTP

<div class="columns"><div>

### HTTP
- protocol that defines how messages are formatted and transmitted

</div><div>

### REST
- architectural style that defines how resources are accessed and manipulated
- built on HTTP

</div>

---
# What is REST?
## REST vs others

<br>

![width:400px left](img/soap-more-polite-rest.jpg)
![height:400px right](img/GraphQL-1.png)



---
# What is REST?
## REST vs others

### Alternatives
- **GraphQL** - query language for APIs that allows clients to request and receive only the data they require, making it more efficient than REST
- **gRPC** - high performance Remote Procedure Call (RPC) framework
- **WebSocket** - a computer communications protocol, providing simultaneous two-way communication channels over a single Transmission Control Protocol (TCP)


---
# REST
## Anatomy of a HTTP request

<br>
<br>

```
POST /api/authors HTTP/1.1            // request line (method URL protocol)

Host: myWebApi.com
Content-Type: application/json        // headers
Cache-Control: no-cache
Authorization: Basic YjhiZWU5ZGNiYzgxODhjNlZjE4YjBkOWIwZjdjZTY=

{
     "Name": "Felipe Gavilán",        // body ('payload')
     "Age": 999
}
```

---
# REST
## Anatomy of a HTTP response

<br>
<br>

```
HTTP/1.1 200 OK                            // status line (code & description)

Date: Thu, 03 Jan 2019 23:26:07 GMT        // headers
Server: gws
Accept-Ranges: bytes
Content-Length: 68894
Content-Type: text/html; charset=UTF-8

<!doctype html><html …                     // response body
```





---
# REST
## Principles

The six guiding principles or constraints of the RESTful architecture are:

- Uniform Interface
- Client/Server
- Stateless
- Cacheable
- _Layered System_
- _Code on Demand (optional)_

---
# REST
## Principle 1: Uniform Interface

### This we want
- **Identification of resources** – uniquely identify each resource involved in the interaction between the client and the server.
- **Manipulation of resources through representations** – resources should have uniform representations in the server response. API consumers should use these representations to modify the resource state in the server.

---
# REST
## Principle: Uniform Interface
### This ...
- **Self-descriptive messages** – Each resource representation should carry enough information to describe how to process the message. It should also provide information of the additional actions that the client can perform on the resource.
- **Hypermedia as the engine of application state** – The client should have only the initial URI of the application. The client application should dynamically drive all other resources and interactions with the use of hyperlinks.

---
# REST
## Principle: Client/Server

Separation of concerns

<div class="columns"><div>

### Client
- User Interface

</div><div>

### Server
- Storage

</div>

---
# REST
## Principle: Statelessness

<div class="box">

Each request from the client to the server must contain all of the information necessary to understand and complete the request.


</div>

For this reason, the client application must entirely keep the session state.


---
# REST
## Principle: Cacheable

<div class="box">

A response should implicitly or explicitly label itself as cacheable or non-cacheable.

</div>

- GET requests should be cachable by default
- POST requests are not cacheable (but can be made cacheable)
- Responses to PUT and DELETE requests are not cacheable at all

---
# REST
## Caching in HTTP

#### Controlling Cache with HTTP Headers
- Expires
  `Expires: Fri, 20 May 2016 19:20:49 GMT`
- Cache-Control
  `Cache-Control: max-age=3600`
- ETag (aka Version-Id)
  `ETag: "abcd1234567n34jv"`
- Last-Modified
  `Last-Modified: Fri, 10 May 2016 09:17:49 GMT`


---
# REST
## Encoding / Compression in HTTP

<div class="box">

Compression, like encryption, is something that happens to the resource representation in transit and must be undone before the client can use the representation.

</div>

---
# REST
## Encoding / Compression in HTTP

### Accept-Encoding (client)

Sample request with _accept-encoding_
```
GET     /employees HTTP/1.1
Host:   www.some-domain.com
Accept: text/html
Accept-Encoding: *
Accept-Encoding: gzip,compress
```

---
# REST
## Encoding / Compression in HTTP

### Content-Encoding (server)

Sample response with content-encoding_
```
200 OK
Content-Type: text/html
Content-Encoding: gzip
```

<br>

<div class="box">

Compression can save a lot of bandwidth, with minimal cost without additional complexity.

</div>

---
# REST
## HTTP Response Codes

The status codes are divided into five categories.

- 1xx: **Informational** – Communicates transfer protocol-level information.
- 2xx: **Success** – Indicates that the client’s request was accepted successfully.
- 3xx: **Redirection** – Indicates that the client must take some additional action in order to complete their request.
- 4xx: **Client Error** – This category of error status codes points the finger at clients.
- 5xx: **Server Error** – The server takes responsibility for these error status codes.

---
# REST
## HTTP Response Codes - the important ones

### Success Codes

<style scoped>
  {
    font-size: 20px
  }
</style>

| Status Code | Description |
| -------- | -------- |
| 200 OK   | Indicates that the request has succeeded.   |
| 201 Created   | Indicates that the request has succeeded and a new resource has been created as a result. Typically, the URI of the newly created resource is returend in the Location Header |
| 204 No Content | The server has fulfilled the request but does not need to return a response body. |


---
# REST
## HTTP Response Codes - the important ones

<br>

### Client Error Codes

<style scoped>
  {
    font-size: 20px
  }
</style>

| Status Code | Description |
| -------- | -------- |
| 400 Bad Request  | The request could not be understood by the server due to incorrect syntax.   |
| 401 Unauthorized | Indicates that the request requires user authentication information.   |
| 403 Forbidden | Unauthorized request. The client does not have access rights to the content. Unlike 401, the client’s identity is known to the server. |
| 404 Not Found | The server can not find the requested resource. |
| 409 Conflict | The request could not be completed due to a conflict with the current state of the resource. |
| 418 I’m a teapot | It was defined as April’s fool joke and is not expected to be implemented by actual HTTP servers. |

---
# REST
## HTTP Response Codes - the important ones

### Server Error Codes

<style scoped>
  {
    font-size: 20px
  }
</style>

| Status Code | Description |
| -------- | -------- |
| 500 Internal Server Error  | The server encountered an unexpected condition that prevented it from fulfilling the request.   |
| 503 Service Unavailable | The server is not ready to handle the request. |

---
# REST
## HTTP Example Error Payload

ResourceReplacement request
```JSON
{
    "message": "Unauthorized",
    "error": "Token exchange failure.  Likely expired or invalid token. 
              Common other problems are incorrect roles or on-prem services that 
              are not running correctly",
    "timestamp": "2024-03-25T12:15:32.264+00:00",
    "status": 401
}
```

---
# Conventions

<br><br>

![center](img/8548d48e6a39d71ba0d24935f33d3123.jpg)

---
# Conventions
## Naming Resources

<div class="box">

Use nouns to represent resources.
Use plural for collection resources.

</div>

```
/customers                // is a collection resource
/customers/{id}           // is a singleton resource
/customers/{id}/accounts  // is a sub-collection resource

/customers/{customerId}/accounts/{accountId}

/device-management/managed-devices
/device-management/managed-devices/{device-id}
```

---
# Conventions
## Naming URIs
<style scoped>
  {
    font-size: 22px
  }
</style>
<br><br>

- do not use trailing forward slash (/) in URIs
```
http://api.example.com/device-management/managed-devices/
http://api.example.com/device-management/managed-devices  /* This is better */
```

- Use hyphens (-) to improve the readability of URIs
```
http://api.example.com/devicemanagement/manageddevices/
http://api.example.com/device-management/managed-devices  /* This is better */
http://api.example.com/deviceManagement/managedDevices/
```

- Do not use underscores ( _ ) in URIs
```
http://api.example.com/device_management/managed_devices
http://api.example.com/device-management/managed-devices  /* This is better */
```
---
# Conventions
## HTTP request methods

- Never use CRUD function names in URIs - use HTTP request methods instead

```
GET /device-management/managed-devices          // Get all devices
POST /device-management/managed-devices         // Create new Device

GET /device-management/managed-devices/{id}     // Get device for given Id
PUT /device-management/managed-devices/{id}     // Update device for given Id
DELETE /device-management/managed-devices/{id}  // Delete device for given Id
```

---
# Conventions
## Query parameters

- Use query parameters to filter URI collection ...

```
/device-management/managed-devices
/device-management/managed-devices?region=CH
/device-management/managed-devices?region=CH&brand=xiaomi
/device-management/managed-devices?region=CH&brand=xiaomi&sort=installation-date
/device-management/managed-devices?region=CH&from=2024-03-01&to=2024-03-31
```

<br>

- or to define which parts of the resource are relevant:

---
# Conventions
## Query parameters

<style scoped> 
p {
  font-size: 18px;
  background: white
}
pre {
  font-size: 15px;
  width:80%
}
</style>

<br>
<br>
<br>
<br>

<div class="columns"><div>

`GET /employees/13423`

```JSON
{
  "email_private": null,
  "email_secure": null,
  "phone_mobile": null,
  "phone_private": "044 422 53 10",
  "phone_number_other": null,
  "id": 3768,
  "personnel_number": "293087",
  "first_name": "Andrea",
  "last_name": "Rennhard",
  "date_of_birth": null,
  "email_business": "renan@zinc.ch"
}
```

</div><div>

`GET /employees/13423?includeShiftPlannerPermissions=true`

```JSON
{
  "email_private": null,
  "email_secure": null,
  "phone_mobile": null,
  "phone_private": "044 422 53 10",
  "phone_number_other": null,
  "shift_planner_permissions": {
    "shift_trade": {
      "approve_shift_trade": false
    },
    "resource_replacement": {
      "start_resource_replacement": false
    },
    "pool": {
      "manage_pools": false,
      "book_pool_employee": false
    }
  },
  "id": 3768,
  "personnel_number": "293087",
  "first_name": "Andrea",
  "last_name": "Rennhard",
  "date_of_birth": null,
  "email_business": "renan@zinc.ch"
}
```



</div></div>


---
# Conventions
## Thou shalt not use verbs in the URI

- Do not Use Verbs in the URI

If we use verbs in the URI, we are most probably creating an RPC-style method call having a JSON request/response format. It would be incorrect to call it REST.

```
/device-management/managed-devices/{id}/scripts/{id}/execute   // It is RPC, and not REST

alternatives:
PUT /device-management/managed-devices/{id}/scripts/{id}       // PUT request with 'status=executing'
POST /device-management/managed-devices/{id}/executing-scripts // POST request with URI of script
```

---
# Conventions
## JSON
<br>
We use a mixture of snake_case and camelCase in our APIs.


- camelCase seem natural in Java & JavaScript
- snake_case might be better suited for case insensitive languages like Delphi

<br>
<div class="box">

Thou shalt use camelCase! &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  [API+Development+and+Documentation](https://polypoint.atlassian.net/wiki/spaces/PGA/pages/3872888105/API+Development+and+Documentation)
<div class="fs20">
https://polypoint.atlassian.net/wiki/spaces/PGA/pages/3872888105/API+Development+and+Documentation
</div>
</div>

---
# Conventions
## API-Versioning

<div class="box">

Change in an API is inevitable.
</div>

- **Breaking changes** should always result in a change to the major version number for an API
- **Non-breaking changes**, such as adding new endpoints or new response parameters, do not require a change to the major version number

---
# Conventions
## API-Versioning Strategies

<br>

### URI Versioning

i.e. `https://placement.polypointservices.ch/api/v1/availabilities`

Used in our APIs. Not very RESTful (different URIs refer to same resource). Will duplicate endpoints if multiple versions need to be supported.

### Versioning using Custom Request Header or Accept Header

Client asks for specific version. Endpoints will need to serve the payload for different versions:
<style scoped>
  {
    font-size: 22px
  }
</style>
```
Accept-version: v1
Accept-version: v2

Accept: application/vnd.polypoint.v1+json
Accept: application/vnd.polypoint+json;version=1.0
```

---
# Conventions
## Long Running Tasks 1/3
<style scoped>
  {
    font-size: 20px
  }
</style>
A long-running task is an operation that requires a considerable amount of server resources and/or time. After submitting the task, the client needs to poll to a provided URL for the task execution progress.

- Resource Creation and Initiation

```json
POST /api/tasks

Status: 202 Accepted
Location: /tasks/12345
Content-Type: application/json

{
  "taskId": 12345,
  "status": "pending",
  "createdAt": "2023-11-04T10:00:00Z"
}
```

---
# Conventions
## Long Running Tasks 2/3
<style scoped>
  {
    font-size: 20px
  }
</style>

- Task Status Resource

```json
GET /api/tasks/12345

Status: 200 OK
Content-Type: application/json
{
  "taskId": 12345,
  "status": "in progress",
  "createdAt": "2023-11-04T10:00:00Z",
  "progress": {
    "percentage": 45,
    "currentStep": "data transformation"
  }
}
```

---
# Conventions
## Long Running Tasks 3/3
<style scoped>
  {
    font-size: 20px
  }
</style>

- Result Reporting

```json
GET /api/tasks/12345

Status: 200 OK
Content-Type: application/json
{
  "taskId": 12345,
  "status": "completed",
  "createdAt": "2023-11-04T10:00:00Z",
  "completedAt": "2023-11-04T10:15:00Z",
  "result": {
    "newResource": "https://example.com/resources/123-456"
  }
}
```

---
# Security

![center](img/E1mpmajXsAEw-dE.jpg)

---
# Security

<div class="box">

Each API request should come with some sort of authentication credentials that must be validated on the server for every request.
</div>

---
# Security
## Best Practices 1/2

<br>

- Keep it Simple
- Always use HTTPS
- Never expose information on URLs
  - hesitate to expose internal APIs

What will you do when you see this URL?
```
GET https://shared-services.polypoint.ch/api/salaries?mitarbeiter=12
```
👉 use GUIDs rather that sequences in URLs

---
# Security
## Best Practices 2/2

<br>

- Consider OAuth2
- Consider Adding TimeStamps in Requests - to prevent basic replay attacks
- Input Parameter Validation

---
# Security
## OAuth2

<div class="box">

The **OAuth 2.0 authorization framework** is a protocol that allows a user to **grant a third-party web site or application access to the user's protected resources**, without necessarily revealing their long-term credentials or even their identity.
</div>

---
# Security
## OAuth2

<br><br><br>

![left height:400px](img/oauth2-flow.png)
![right height:400px](img/oauth2.png)

---
# Security
## OpenID Connect (OIDC)

<div class="box">

**OpenID Connect** is a simple **identity layer on top of the OAuth 2.0 protocol**.

OIDC is used for **authentication** as it augments OAuth 2.0 by adding standard identity tokens. It can be used for SSO.

OIDC uses **JWT** (JSON Web Tokens) to represent claims about the authenticated user. 

</div>

---
# Security
## OpenID Connect (OIDC) - example flow

<br>
<br>

![center width:700px](img/acfwpkce.png)

---
# Security
## JWT (JSON Web Token - "dʒɒt")

<div class="box">

JSON Web Token (JWT) defines a compact way for securely transmitting information between parties as a JSON object. 
This information **can be verified and trusted** because it is **digitally signed.**

</div>

---
# Security
## JWT (JSON Web Token - "dʒɒt")

JSON Web Tokens consist of three parts separated by dots (.): 
`header.payload.signature`

These parts are individual JSON objects which are then *Base64Url* encoded

---
# Security
## JWT - Example PolyId Access Token:

<style scoped> 
p {
  margin-top: 80px;
  font-size: 12px; 
}
</style>

eyJhbGciOiJSUzI1NiIsImtpZCI6ImM5NTcxNTFmLWUwNTItNGMyYS1hZTJhLTBmNzJkYjdiZmEyOSIsInR5cCI6IkpXVCJ9
.
eyJhdWQiOlsiaHR0cHM6Ly9jdC1wb21hLXdlYmFwcC5rOHMucG9seXBvaW50LWRldi5jb20vaGVhbHRoLXByb2Zlc3Npb25hbC1hcHAvIl0sImNsaWVudF9pZCI6IjBmNTZmNGYyLTY3OWEtNGI0Yi1hZjMwLThhMmZiZThjMGY2MCIsImV4cCI6MTcxMTE5Nzc5NSwiZXh0Ijp7ImVtYWlsIjoiamFuLnJldXRpbWFubkBnbWFpbC5jb20iLCJmYW1pbHlfbmFtZSI6IlJlbm5oYXJkIiwiZ2l2ZW5fbmFtZSI6IkFuZHJlYSIsImlkIjoiY3QtemluYy1tYXN0ZXIyLXRlc3RfNTI3MmZkYjItNmI1MC00NDZjLTk4NzQtZWMxMzJlZjg2ZGQzIiwibG9jYWxlIjoiZGUiLCJwZXBfZGF0YSI6eyJwZXBfZW1wbG95ZWVfaWQiOiIzNzY4IiwicGVwX2VtcGxveW1lbnRzIjpbeyJjb2xvciI6IiNFODZDMEUiLCJlbXBsb3ltZW50X251bWJlciI6IjAxIiwiZW5kX2RhdGUiOlsyOTk5LDEyLDMxXSwiaWQiOiI0NDU4Iiwic3RhcnRfZGF0ZSI6WzIwMTQsMiwxXX1dLCJwZXBfcGVybWlzc2lvbnMiOnsicG9vbCI6eyJib29rX3Bvb2xfZW1wbG95ZWUiOmZhbHNlLCJtYW5hZ2VfcG9vbHMiOmZhbHNlfSwicmVzb3VyY2VfcmVwbGFjZW1lbnQiOnsic3RhcnRfcmVzb3VyY2VfcmVwbGFjZW1lbnQiOmZhbHNlfSwic2NoZWR1bGluZ19yZXF1ZXN0Ijp7ImNyZWF0ZV9zY2hlZHVsaW5nX3JlcXVlc3QiOnRydWUsImRlbGV0ZV9zY2hlZHVsaW5nX3JlcXVlc3QiOnRydWV9LCJzaGlmdF90cmFkZSI6eyJhcHByb3ZlX3NoaWZ0X3RyYWRlIjpmYWxzZSwic3VibWl0X3NoaWZ0X3RyYWRlIjp0cnVlfSwidGltZV9yZWNvcmRpbmciOnsiYWxsb3dfdGltZV9zdGFtcHMiOmZhbHNlLCJjYW5fY3JlYXRlX2Jsb2NrcyI6ZmFsc2UsImNhbl9kZWxldGVfYmxvY2tzIjpmYWxzZSwiY2FuX3VwZGF0ZV9ibG9ja3MiOmZhbHNlLCJlbmFibGVkIjpmYWxzZX19fSwicHJlZmVycmVkX3VzZXJuYW1lIjoicmVuYW4iLCJwcm92aWRlcklkIjo5NSwicHJvdmlkZXJOYW1lIjp7ImRlIjoiY3QtemluYy1tYXN0ZXIyLXRlc3QiLCJlbiI6ImN0LXppbmMtbWFzdGVyMi10ZXN0IiwiZnIiOiJjdC16aW5jLW1hc3RlcjItdGVzdCIsIml0IjoiY3QtemluYy1tYXN0ZXIyLXRlc3QifSwicm9sZXMiOlsiUk9MRV9IRUFMVEhfUFJPRkVTU0lPTkFMIiwiUk9MRV9URU5BTlRfQURNSU4iXSwic2lkIjoiMWUyNDY1YTktZGI0ZS00MTdmLTg4YjEtYjcwMGJlOWVjNmQ0Iiwic3ViIjoiMjU0LjUyNzJmZGIyLTZiNTAtNDQ2Yy05ODc0LWVjMTMyZWY4NmRkMyIsInRlbmFudEV4cCI6MS43MTExOTQ0OTNlKzA5LCJ0ZW5hbnRJZCI6ImN0LXppbmMtbWFzdGVyMi10ZXN0IiwidGVuYW50X2lzc3VlciI6Imh0dHBzOi8vY3QtemluYy1tYXN0ZXIyLWlkbS5wb2x5cG9pbnQuZGV2OjgzODMvYXV0aC9yZWFsbXMvUE9MWVAwMSIsInRlbmFudF9zdWJqZWN0IjoiNTI3MmZkYjItNmI1MC00NDZjLTk4NzQtZWMxMzJlZjg2ZGQzIiwidGVzdGVyIjpmYWxzZX0sImlhdCI6MTcxMTE5NDE5NSwiaXNzIjoiaHR0cHM6Ly9jdC1wb2x5aWQuazhzLnBvbHlwb2ludC1kZXYuY29tIiwianRpIjoiNGI3NmZiZDctNzYxMi00MzVlLWIzNDgtMzhiMWM3ZDk0MzkxIiwibmJmIjoxNzExMTk0MTk1LCJzY3AiOlsib3BlbmlkIl0sInN1YiI6IjI1NC41MjcyZmRiMi02YjUwLTQ0NmMtOTg3NC1lYzEzMmVmODZkZDMifQ
.
vHFYWMSOmYQz7SX7R_FKgVQbpp-kekPzYtwnIahdGZfRrNV-4KKdTlbv0uFjiMq7v0mn5v3Q3d4Rp5T6SJUbS2Qz0CuxbW4mkGO6hJ9e236P5_GCJFodyK7a-b3759pEMlKzs2zV4KYo7qoyYMPdiyAieS-KFYFST_fcbIm1Oqw4a_VQhytezmA72ohptNzJ9KeiVhEIyP97ACiANHnkGLyMISXGaH-EyZEeJZyJ7mXIMChFy3QjSjvfKUO7Re-Lat5DF5WOJxGW1wkyXrw3S7i0zE4wAIbNpmGf0SlSDCqo-fpLFoH5CAD8v_917rmX9armKrt1hRaOJlBoE-M5cKtSa5pVMl23PUKWZuJ9INKgnStdxRa303p8UzgJ2IR9KI1Hn6QKBHSx56edqvRfvnCl2IraajfTfnB3SMcSWhGxlUIG3TwB0mCSbiUGbDj1B4FqQJGarfT_vqpIPb8yrNYwM-3LWaa_xqiDisCXO9joJOWVHNAA5VR9_Smnxzc24i31hgVTLWwwFe_2Li4YK_9fMG5ClVZn7yX4tD1UQ-fnR62UqSeezgRaII3uYxSRfSulfsOZ8VOkmArRqOburf9VQueyzhfqPG3dIPAZgfqSz4AsUq8lY5p2fDu-XEm0Uo8p8J1iHCb6gmZ05nDsQcHYKYUu0PvfxAembr4Y-Kk


---
# Security
## JWT - Example Payload

<style scoped> pre {  margin-top: 80px;  font-size: 16px; width: 900px} </style>

```json
{
  "aud": [
    "https://ct-poma-webapp.k8s.polypoint-dev.com/health-professional-app/"
  ],
  "client_id": "0f56f4f2-679a-4b4b-af30-8a2fbe8c0f60",
  "exp": 1711197795,
  "ext": {
    "email": "jan.reutimann@gmail.com",
    "family_name": "Rennhard",
    "given_name": "Andrea",
    "id": "ct-zinc-master2-test_5272fdb2-6b50-446c-9874-ec132ef86dd3",
    "locale": "de",
    "pep_data": {
      ...
    },
    "preferred_username": "renan",
    ...
    "roles": [
      "ROLE_HEALTH_PROFESSIONAL",
      "ROLE_TENANT_ADMIN"
    ],
    "sid": "1e2465a9-db4e-417f-88b1-b700be9ec6d4",
    ...
}
```

---
# Security
## HTTP Request with OIDC Access Token


<br>
<br>

![center width:800px](img/SCR-20240323-lsjd.png)

---

![center width:600px](img/thankyou2.webp)

---

# WEB Resources
* https://restfulapi.net
* https://en.wikipedia.org/wiki/REST
* https://polypoint.atlassian.net/wiki/spaces/PGA/pages/3872888105/API+Development+and+Documentation


### These slides
* https://jreutimann.github.io/slides/rest
  
