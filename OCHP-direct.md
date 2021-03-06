![Open Clearing House Protocol (OCHP)](http://www.ochp.eu/wp-content/uploads/2015/02/OCHPlogo.png)

 OCHP-direct Extension
 
 * * *



## Protocol Release Log

Prot. Version | Date       | Comment
:-------------|:-----------|:-------
0.1           | 27‑03‑2012 | Concept, Functional specification. Commit: [77cccd838db692ab6f8b77fb4be8e81d59ec04e2](../../commit/77cccd838db692ab6f8b77fb4be8e81d59ec04e2)


Copyright (c) 2015 smartlab, bluecorner.be, e-laad.nl

Permission is hereby granted, free of charge, to any person obtaining a 
copy of this software and associated documentation files 
(the "Software"), to deal in the Software without restriction, 
including without limitation the rights to use, copy, modify, merge, 
publish, distribute, sublicense, and/or sell copies of the Software, 
and to permit persons to whom the Software is furnished to do so, 
subject to the following conditions:

The above copyright notice and this permission notice shall be included 
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS 
OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF 
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. 
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY 
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, 
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE 
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


 * * *

# Contents

- [Preface](#preface)
    - [Conventions](#conventions)
- [Introduction](#introduction)
    - [Use Cases of OCHP direct](#use-cases-of-ochp-direct)
            - [Basic use cases](#basic-use-cases)
            - [Advanced use cases](#advanced-use-cases)
    - [Basic Principles of OCHP direct](#basic-principles-of-ochp-direct)
        - [Trust and authorisation structure](#trust-and-authorisation-structure)
            - [The usual situation without OCHP direct](#the-usual-situation-without-ochp-direct)
            - [The situation with OCHP direct](#the-situation-with-ochp-direct)
            - [Security of the OCHP direct interface](#security-of-the-ochp-direct-interface)
            - [Identification token distribution](#identification-token-distribution)
- [Partner-to-CHS interface description (Extension to OCHP)](#partner-to-chs-interface-description-extension-to-ochp)
    - [Exchange of interface definition _Basic_](#exchange-of-interface-definition-basic)
        - [Set own interface definition in the CHS](#set-own-interface-definition-in-the-chs)
        - [Get roaming partners interface definitions from the CHS](#get-roaming-partners-interface-definitions-from-the-chs)
- [Partner-to-partner interface description (_OCHP direct_ interface)](#partner-to-partner-interface-description-ochp-direct-interface)
    - [Get status information of charge points _Basic_](#get-status-information-of-charge-points-basic)
        - [Request current live status from an Operator](#request-current-live-status-from-an-operator)
        - [Report a data or compatibility discrepancy to an Operator](#report-a-data-or-compatibility-discrepancy-to-an-operator)
    - [Start, stop and control a charging process remotely _Basic_](#start-stop-and-control-a-charging-process-remotely-basic)
        - [Select a charge point of an operator](#select-a-charge-point-of-an-operator)
        - [Control a selected charge point in an operator's backend](#control-a-selected-charge-point-in-an-operators-backend)
        - [Release a selected charge point in an operator's backend](#release-a-selected-charge-point-in-an-operators-backend)
    - [Inform a provider about a charging process _Advanced_](#inform-a-provider-about-a-charging-process-advanced)
        - [Send charging process information of a provider's customer](#send-charging-process-information-of-a-providers-customer)
- [Messages](#messages)
    - [Messages for the exchange of interface definitions](#messages-for-the-exchange-of-interface-definitions)
        - [AddServiceEndpoints.req](#addserviceendpointsreq)
        - [AddServiceEndpoints.conf](#addserviceendpointsconf)
        - [GetServiceEndpoints.req](#getserviceendpointsreq)
        - [GetServiceEndpoints.conf](#getserviceendpointsconf)
    - [Messages for the _OCHP direct_ interface](#messages-for-the-ochp-direct-interface)
        - [DirectEvseStatus.req](#directevsestatusreq)
        - [ReportDiscrepancy.req](#reportdiscrepancyreq)
        - [ReportDiscrepancy.conf](#reportdiscrepancyconf)
        - [SelectEvse.req](#selectevsereq)
        - [SelectEvse.conf](#selectevseconf)
        - [ControlEvse.req](#controlevsereq)
        - [ControlEvse.conf](#controlevseconf)
        - [ReleaseEvse.req](#releaseevsereq)
        - [ReleaseEvse.conf](#releaseevseconf)
        - [InformProvider.req](#informproviderreq)
        - [InformProvider.conf](#informproviderconf)
- [Types](#types)
    - [Types that extend the OCHP interface _Basic_](#types-that-extend-the-ochp-interface-basic)
        - [DirectEndpoint *class*](#directendpoint-class)
        - [ProviderEndpoint *class*](#providerendpoint-class)
        - [OperatorEndpoint *class*](#operatorendpoint-class)
        - [ContractPattern *class*](#contractpattern-class)
        - [EvsePattern *class*](#evsepattern-class)
    - [Types for the _OCHP direct_ interface](#types-for-the-ochp-direct-interface)
        - [DirectResult *enum*](#directresult-enum)
        - [DirectResultCodeType *enum*](#directresultcodetype-enum)
        - [DirectId *class*](#directid-class)
        - [DirectOperation *enum*](#directoperation-enum)
        - [DirectMessage *enum*](#directmessage-enum)
- [Binding to Transport Protocol](#binding-to-transport-protocol)
    - [OCHP direct over SOAP](#ochp-direct-over-soap)
    - [Partner Identification](#partner-identification)


 * * *
 


# Preface

This document defines an extension to the Open Clearing House Protocol 
(OCHP). For more information visit [ochp.eu](http://ochp.eu).


## Conventions

_This extesnsion follows the same conventions as OCHP:_

The key words *must*, *must not*, *required*, *shall*, *shall
not*, *should*, *should not*, *recommended*, *may* and
*optional* in this document are to be interpreted as described in
[https://tools.ietf.org/html/rfc2119](RFC 2119).

The cardinality is defined by the indicators _*_, *+*, *?* and
*1*, where the last one is the default. The meaning and mapping to
XML syntax is as follows:

Meaning      | XML Schema                                    | DTD
:------------|:----------------------------------------------|---
At most one  | `minOccurs="0" maxOccurs="1"`                 | ?
one or more  | `minOccurs="1" maxOccurs="unbounded"`         | +
zero or more | `minOccurs="0" maxOccurs="unbounded"`         | *
exactly one  | *(default)*                                   | 1

For some data fields a [http://en.wikipedia.org/wiki/Regular_expression](Regular Expression) is
provided as an additional but very precise definition of the data
format.

The character *>* in front of any data field indicates a choice of 
multiple possibilities.

The character *~* appended to any data field indicates the 
implementation as XML attribute instead of an element.





# Introduction


For a general introduction to the OCHP, see the introduction section in 
the [OCHP documentation](OCHP.md).



## Use Cases of OCHP direct

The overall use case for this interface is to control services like 
charging sessions in an operator's backend while handling the user 
authorisation within an (from the operator's point of view) external 
system.

The customer story can be shortened to: 

> One (provider) app for all charging stations – regardless of their operator.

This generic main use cases splits up in several sub parts. Those are:

#### Basic use cases
 * **Remote Start:** A user starts a charging process at an operator‘s 
   charge pole by using a provider‘s app. They are starting the process 
   from a – of the operator's point of view – remote service.
 * **Remote Stop:** A user stops a charging process at an operator‘s 
   charge pole by using a provider‘s app (that was remotely started).
 * **Live Info:** A user requests information about a charging process 
   at an operator's charge pole by using a provider's app (from which 
   the process was started).

#### Advanced use cases
 * **Charge Event:** A user gets informed by a provider's app about 
   status changes of a charging process at an operator's charge pole, 
   even if it wasn't started remotely.
 * **Remote Control:** A user controls a charging process at an 
   operator‘s charge pole that was not remotely started by using a 
   provider‘s app.
 * **Remote Action:** A user triggers advanced and not charging process 
   related actions at a charge point or charging station of an operator.

The __basic use cases__ require the operator to act as a server in 
order to receive information and commands from the provider. The
__advanced use cases__ require also the provider to act as a server.




## Basic Principles of OCHP direct

The OCHP direct Interface describes a set of methods to control 
charging sessions in an EVSE operator's backend. While dedicated 
methods in the clearing house's interface extend its functionality to 
provide remote services, the actually service requests are sent between 
the operator and the provider directly. In those cases the operator 
backend acts as a server, in contrast to pure clients as common for all 
other OCHP communication. The backward communication, from the operator 
system to the provider system is also possible. In that case the 
provider system will act as a SOAP server and the operator system as 
the client.

The following Figure illustrates the communication paths of OCHP direct.
The extending messages of OCHP allow the publication of backend 
specification and the discovery of roaming partner's backends.

![Figure OCHP direct Communication Overview](media/OCHPdirectCommunicationOverview.png "OCHP direct Communication Overview")

The backend specification is send and updated regularly. It contents 
all properties that describe the roaming partner's backend:

 * The URL of the backend's OCHP direct endpoint(s).
 * The security token of the backend. (See chapter [Security of the OCHP direct interface](#security-of-the-ochp-direct-interface) 
   for more information.)
 * All business objects that are operated by this backend, represented 
   by blacklists and/or whitelists.

This data can be mapped onto a data structure as illustrated in the 
following figure *OCHP direct ER Model*. The depictured data structure 
allows for dynamic updates of endpoints and partner-tokens, which is 
necessary to guaranty an uninterrupted service.

Remarkable about the data model is the absence of an backend entity. 
All related entities are bound to the roaming partner (operator or 
provider), identified by their IDs. Thus, each roaming partner is free 
to operate their services on one or multiple backend systems or even 
share one backend system with another roaming partner. This should 
cover all possible market situations. 

![Figure OCHP direct ER Model](media/OCHPdirectErModel.png "OCHP direct ER Model")



### Trust and authorisation structure

Based on the assumption that _OCHP direct_ is used in addition to a 
regular OCHP connection via a clearing house, the following trust 
structure applies. However, _OCHP direct_ may also be used in other 
combinations, where the OCHP-part of the following description is to be 
covered by alternative methods.


#### The usual situation without OCHP direct

When two roaming partners connect via OCHP and the clearing house, the 
authorisation and trust structure can be defined as follows:

 * Both roaming partners trust the clearing house
 * The operator authorises the provider to use their charging stations
   generally, by setting the roaming connection in the clearing house
 * The provider authorises and trusts the operator to authorise the
   provider's customers generally
 * The operator authorises the provider's customers at their charging
   stations for inividual charging sessions

In this situation the single authorisation is be done in the operator's 
backend _on behalf of_ the provider. The provider trusts the operator 
that all sent customer tokens are getting authorised on all charge 
points or as based on the contract between both.


#### The situation with OCHP direct

When direct authorisation requests come in place, the situation turns 
around:

 * Both roaming partners trust the clearing house
 * The operator authorises the provider to use their charging stations
   generally, by setting the roaming connection in the clearing house
 * __The operator authorises and trusts the provider to authorise their
   customers at the operator's charge points generally__
 * _The provider authorises their own customers at the charging stations 
   of the operator for inividual charging sessions_

In this new situation the operator gives the responsiblity to authorise 
charging sessions away to the provider. A operator therefore should not 
decline an remote authorisation for other than contractual or technical 
valid reasons.


#### Security of the OCHP direct interface

Each roaming partner who makes use of OCHP direct needs provide a SOAP 
server with a public accessible interface. It is obvious that they must secure
those interfaces to:
 * restrict usage only to their current roaming partners and
 * secure the transmitted data.

This applies to the operator and for advanced use cases also to the 
provider.

Therefore the interfaces must be protected on HTTP transport layer 
level, using SSL and Basic Authentication. Please note that this 
mechanism does **not** require client side certificates for 
authentication, only server side certificates in order to provide a 
secure SSL connection.

The OCHP direct interface of every roaming partner must be secured via 
*TLS 1.2* ([RFC6176](http://tools.ietf.org/html/rfc6176)).

The identification of the requester and the access restriction of the 
interface is done by rotating identification tokens which are 
distributed via the clearing house. (See 
[Identification token distribution](#identification-token-distribution) 
for further information.)

Each request to a OCHP direct interface must contain a *Authorization* 
HTTP header:

```http
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
```

The hash value in this header is composed through the following steps:
 1. The identification tokens of sender and receiver are combined into 
    a string "receiver-token:sender-token"
 2. The resulting string is then encoded using the RFC2045-MIME variant 
    of Base64 ([RFC1945](http://tools.ietf.org/html/rfc1945#section-11)
 3. The authorization method and a space i.e. `Basic␣` is then put 
    before the encoded string.

The OCHP direct endpoint should check for valid authorisation in order 
to prevent unintended usage of their endpoints or cyber-attacks.


#### Identification token distribution

The partner's tokens for identification and authorisation are exchanged 
and distributed through the clearing house. Based on the set roaming 
connections the tokens are made available. Each token is valid for a 
period of full calendar days, synchronus to the UTC time.

This mechanism is used to guarantee uninterupted service in combination 
with a high security level and compatibility with the majority of 
systems. The synchronisation and token-exchange-cycle is as follows.
On day `N` do:

 1. *At 00:30 UTC:* Invalidate/delete all tokens of day `N-1`.
 2. Generate new own token for day `N+1`.
 3. *Before 12:00 UTC:* Send/upload own token for day `N+1`.
 4. *After 12:00 UTC:* Fetch/download partner's tokens for day `N+1`.
 5. Generate token combinations for day `N+1` from own and partner's 
    tokens. Here `AB2`.
 6. *At 23:50 UTC:* Make token combinations for day `N+1` valid.

![Figure OCHP direct Token Exchange](media/OCHPdirectTokenExchange.png "OCHP direct Token Exchange")



# Partner-to-CHS interface description (Extension to OCHP)

This interface description extends the OCHP protocol. The additional
methods are available in the interface of the Clearing House.



## Exchange of interface definition _Basic_



### Set own interface definition in the CHS

The backend of each roaming partner has to send the definition of its 
OCHP direct interface to the Clearing House to share that data with 
their connected roaming partners. The upload of the own interface 
definition is done in the following way:

 * CMS or MDM sends the AddServiceEndpoints.req PDU.
 * CHS responds with a AddServiceEndpoints.conf PDU.



### Get roaming partners interface definitions from the CHS

The backend of each roaming partner has to fetch the global list of
interface definitions from the CHS. The download of all interface
of connected partners is done in the following way:

 * CMS or MDM sends the GetServiceEndpoints.req PDU.
 * CHS responds with GetServiceEndpoints.conf PDU.






# Partner-to-partner interface description (_OCHP direct_ interface)

This interface description is the core of OCHP-direct. The described
methods must be available at the operator's backend _CMS_ (basic use
case) and the provider's backend _MDM_ (advanced use case).


## Get status information of charge points _Basic_



### Request current live status from an Operator

The backend of a provider may request the current live status for a list
of EVSEs. This allows for a lower latency than the status distribution
via the Clearing House.

 * MDM sends the GetEvseStatus.req PDU.
 * CMS responds with a GetStatus.conf PDU.
   See OCHP/[GetStatus.conf](OCHP.md#getstatus-conf)


### Report a data or compatibility discrepancy to an Operator

The backend of a provider may report an issue concerning the data or
the compatibility or status of an EVSE to the operator to their
information. The operator may decide how to handle the report.

 * MDM sends the ReportDiscrepancy.req PDU.
 * CMS responds with a ReportDiscrepancy.conf PDU.




## Start, stop and control a charging process remotely _Basic_

The remote operation of a charging process in a operator backend is
beeing done in three steps (basic use cases):

 1. Selection of the charge point
 2. Controling of the charge point
 3. Release of the charge point

In step (1) a OCHP-direct session ID is generated by the operator and
returned. This ID must be used in step (2) and (3). Step (2) may be
repeated to change the parameters. The session is ended in step (3).

This makes it possible to maintain a mutal charging session in two
systems.

The following figure gives an overview of the communication. After step
(3) follows the CDR exchange process as described in OCHP. Calls from
the operator to the provider (_italic_) are covered in the advanced use
cases.

![Figure OCHP direct basic process](media/OCHPdirectProcess-1.png "OCHP direct basic process")


### Select a charge point of an operator

Before a charging process can be started, the provider needs to select
an EVSE in an operator's backend. This starts the session and generates
the OCHP-direct session ID. The operator must reserve the selected
charging station for the communicated Contract-ID.

 * MDM sends the SelectEvse.req PDU.
 * CMS responds with a SelectEvse.conf PDU.


### Control a selected charge point in an operator's backend

When a EVSE was selected it can be controlled in the limits of the
charging process by the provider.

 * MDM sends the ControlEvse.req PDU.
 * CMS responds with a ControlEvse.conf PDU.

**Note:** The control method _ControlEvse.req_ can be called one to multiple times during a charging session. It's parameters define weather the provider requests a start or end of the charging session or if they want to change it (in terms of it's parameters). The operator shall handle all three operations and respond to it's capabilities to actually execute the request accordingly. 


### Release a selected charge point in an operator's backend

After the end of the charging process the provider should release the
prior selected EVSE.

 * MDM sends the ReleaseEvse.req PDU.
 * CMS responds with a ReleaseEvse.conf PDU.

**Note:** To end a charging session properly, the provider should always call _ReleaseEvse.req_. Alternatively, they can also call _ControlEvse.req_ with the parameter _operation='end'_ to explicitly end a charging process. A session with no ongoing charging process (_ControlEvse.req_ with _operation='start'_ was not called or not successful) shall always be closed with _ReleaseEvse.req_.
When the operator receives a valid _ReleaseEvse.req_ for an ongoing charging process which was not ended with a call to _ControlEvse.req_ with parameter _operation='end'_, the operator should implicitly end the process.



## Inform a provider about a charging process _Advanced_

The provider should get informed about any status updates to an 
OCHP-direct charging process. The operator's backend must make use of
a treashold in order to avoid too many messages.

The information types are:
 * Start of a charging session. _The car is properly connected and the process was authorized._
 * End of a charging session. _The charging session has ended by any event and will not be resumed. The car may still be plugged in._
 * Metering information (status)
 * Power management information (status)
 * Invoicing ready, CDR sent (finish)

 
![Figure OCHP direct advanced process](media/OCHPdirectProcess-2.png "OCHP direct advanced process")



### Send charging process information of a provider's customer

When a status update to a charging process gets available, the operator
informs the concerned provider.

 * CMS sends the InformProvider.req PDU.
 * MDM responds with a InformProvider.conf PDU.












# Messages


## Messages for the exchange of interface definitions

These messages are used to exchange the interface definitions of the
OCHP direct interfaces between roaming partners.


### AddServiceEndpoints.req

This contains the field definition of the AddServiceEndpoints.req 
sent by the MDM or CMS towards the CHS.

 Field Name            |  Field Type        |  Card.  |  Description
:----------------------|:-------------------|:--------|:------------
providerEndpointArray  |  ProviderEndpoint  |  *      |  Array of endpoints of the partners provider system.
operatorEndpointArray  |  OperatorEndpoint  |  *      |  Array of endpoints of the partners operator system.


### AddServiceEndpoints.conf

This contains the field definition of the AddServiceEndpoints.conf
sent by the CHS as response to the AddServiceEndpoints.req.

 Field Name  |  Field Type  |  Card.  |  Description
:------------|:-------------|:--------|:------------
result       |  Result      |  1      |  This contains the result of AddServiceEndpoints.req.


### GetServiceEndpoints.req

This contains the field definition of the GetServiceEndpoints.req sent
by a partner's system to the CHS.
No fields are defined.


### GetServiceEndpoints.conf

This contains the field definition of the GetServiceEndpoints.conf
sent by the CHS as response to the GetServiceEndpoints.req.

 Field Name            |  Field Type        |  Card.  |  Description
:----------------------|:-------------------|:--------|:------------
result                 |  Result            |  1      |  This contains the result of GetServiceEndpoints.req.
providerEndpointArray  |  ProviderEndpoint  |  *      |  Array of endpoints of all providers connected to the requester.
operatorEndpointArray  |  OperatorEndpoint  |  *      |  Array of endpoints of all operators connected to the requester.



## Messages for the _OCHP direct_ interface


### DirectEvseStatus.req

This contains the field definition of the DirectEvseStatus.req 
sent by the MDM towards the CMS.

 Field Name      |  Field Type  |  Card.  |  Description
:----------------|:-------------|:--------|:------------
requestedEvseId  |  EvseId      |  +      |  List of EVSE-IDs the live status is requested for.



### ReportDiscrepancy.req

This contains the field definition of the ReportDiscrepancy.req 
sent by the MDM towards the CMS.

 Field Name  |  Field Type    |  Card.  |  Description
:------------|:---------------|:--------|:------------
evseId       |  EvseId        |  1      |  The charge point which is affected by the report.
report       |  string(2000)  |  1      |  Textual or generated report of the discrepancy.



### ReportDiscrepancy.conf

This contains the field definition of the ReportDiscrepancy.conf 
sent by the CMS as a response to ReportDiscrepancy.req.
No fields are defined.



### SelectEvse.req

This contains the field definition of the SelectEvse.req 
sent by the MDM towards the CMS.

 Field Name  |  Field Type  |  Card.  |  Description
:------------|:-------------|:--------|:------------
evseId       |  EvseId      |  1      |  The charge point which is selected by the provider.
contractId   |  ContractId  |  1      |  Contract-ID for which the charge point is selected.



### SelectEvse.conf

This contains the field definition of the SelectEvse.conf 
sent by the CMS as a response to SelectEvse.req.

 Field Name  |  Field Type    |  Card.  |  Description
:------------|:---------------|:--------|:------------
result       |  DirectResult  |  1      |  This contains the result of SelectEvse.req.
directId     |  DirectId      |  ?      |  The session id for this direct charging process on success.
ttl          |  DateTimeType  |  ?      |  On success the time until this selection is valid.



### ControlEvse.req

This contains the field definition of the ControlEvse.req 
sent by the MDM towards the CMS.

 Field Name   |  Field Type       |  Card.  |  Description
:-------------|:------------------|:--------|:------------
directId      |  DirectId         |  1      |  The session id referencing the direct charging process to be controlled.
operation     |  DirectOperation  |  1      |  The operation to be performed for the selected charge point.
maxPower      |  float            |  ?      |  Maximum authorised power in kilowatts. Example: "3.7", "8", "15"
maxEnergy     |  float            |  ?      |  Maximum authorised energy in kilowatthours. Example: "5.5", "20", "85"



### ControlEvse.conf

This contains the field definition of the ControlEvse.conf 
sent by the CMS as a response to ControlEvse.req.

 Field Name  |  Field Type    |  Card.  |  Description
:------------|:---------------|:--------|:------------
result       |  DirectResult  |  1      |  This contains the result of ControlEvse.req.
directId     |  DirectId      |  1      |  The session id for this direct charging process.
ttl          |  DateTimeType  |  ?      |  On success the timeout for this session.



### ReleaseEvse.req

This contains the field definition of the ReleaseEvse.req 
sent by the MDM towards the CMS.

 Field Name  |  Field Type  |  Card.  |  Description
:------------|:-------------|:--------|:------------
directId     |  DirectId    |  1      |  The session id referencing the direct charging process to be released.



### ReleaseEvse.conf

This contains the field definition of the ReleaseEvse.conf 
sent by the CMS as a response to ReleaseEvse.req.

 Field Name  |  Field Type    |  Card.  |  Description
:------------|:---------------|:--------|:------------
result       |  DirectResult  |  1      |  This contains the result of ReleaseEvse.req.
directId     |  DirectId      |  1      |  The session id for this direct charging process.
ttl          |  DateTimeType  |  ?      |  On success the timeout for this session.



### InformProvider.req

This contains the field definition of the InformProvider.req 
sent by the CMS towards the MDM.

 Field Name       |  Field Type     |  Card.  |  Description
:-----------------|:----------------|:--------|:------------
message           |  DirectMessage  |  1      |  The operation to be performed for the selected charge point.
evseId            |  EvseId         |  1      |  The charge point which is used for this charging process.
contractId        |  ContractId     |  1      |  Contract-ID to which the charge point is assigned.
directId          |  DirectId       |  1      |  The session id for this direct charging process.
ttl               |  DateTimeType   |  ?      |  On success the timeout for this session.
maxPower          |  float          |  ?      |  Maximum authorised power in kilowatts. Example: "3.7", "8", "15"
maxEnergy         |  float          |  ?      |  Maximum authorised energy in kilowatthours. Example: "5.5", "20", "85"
currentPower      |  float          |  ?      |  The currently supplied power limit in kilowatts in case of load management. Example: "3.7", "8", "15"
chargedEnergy     |  float          |  ?      |  The amount of energy in kilowatthours transferred during this charging process. Example: "5.5", "20", "85"



### InformProvider.conf

This contains the field definition of the InformProvider.conf 
sent by the MDM as a response to InformProvider.conf.

 Field Name      |  Field Type    |  Card.  |  Description
:----------------|:---------------|:--------|:------------
result           |  DirectResult  |  1      |  This contains the result of InformProvider.req.










# Types


## Types that extend the OCHP interface _Basic_

These data types extend the OCHP interface and are understood by the
Clearing House.


### DirectEndpoint *class*

Contains a generic endpoint definition.

 Field Name    |  Field Type  |  Card.  |  Description
:--------------|:-------------|:--------|:------------
 url           | string(255)  | 1       | The endpoint address.
 namespaceUrl  | string(255)  | ?       | The WSDL namespace definition.
 accessToken   | string(255)  | 1       | The secret token to access this endpoint.
 validUntil    | dateTime     | 1       | The date till when this endpoint/token combination is valid.


### ProviderEndpoint *class*

Contains the endpoint definition of a provider's MDM system.
Expands the DirectEndpoint.

 Field Name    |  Field Type      |  Card.  |  Description
:--------------|:-----------------|:--------|:------------
 url           | string(255)      | 1       | The endpoint address.
 namespaceUrl  | string(255)      | ?       | The WSDL namespace definition.
 accessToken   | string(255)      | 1       | The secret token to access this endpoint.
 validUntil    | dateTime         | 1       | The date till when this endpoint/token combination is valid.
 whitelist     | ContractPattern  | +       | List of patterns that match all Contract-IDs the endpoint is responsible for.
 blacklist     | ContractPattern  | *       | List of patterns that match Contract-IDs the endpoint is not responsible for, but are matched by the whitelist.


### OperatorEndpoint *class*

Contains the endpoint definition of an operator's CMS backend.
Expands the DirectEndpoint.

 Field Name    |  Field Type  |  Card.  |  Description
:--------------|:-------------|:--------|:------------
 url           | string(255)  | 1       | The endpoint address.
 namespaceUrl  | string(255)  | ?       | The WSDL namespace definition.
 accessToken   | string(255)  | 1       | The secret token to access this endpoint.
 validUntil    | dateTime     | 1       | The date till when this endpoint/token combination is valid.
 whitelist     | EvsePattern  | +       | List of patterns that match all EVSE-IDs the endpoint is responsible for.
 blacklist     | EvsePattern  | *       | List of patterns that match EVSE-IDs the endpoint is not responsible for, but are matched by the whitelist.


### ContractPattern *class*

Defines a pattern that matches Contract-IDs. The pattern must be
specified for IDs without optional seperators. The wildcard character
for the pattern is `%`. The pattern as well as the ID is case sensitive.

```regex
[A-Za-z]{2}[A-Za-z0-9]{3}[Cc][A-Za-z0-9]{0,9}%?
```


### EvsePattern *class*

Defines a pattern that matches EVSE-IDs. The pattern must be
specified for IDs without optional seperators. Seperators in the ID's
instance are handled as part of the alphabet. The wildcard character
for the pattern is `%`.

```regex
[A-Z]{2}[A-Z0-9]{3}[E]([A-Z0-9]?[A-Z0-9\*]{0,30})%?
```


## Types for the _OCHP direct_ interface


### DirectResult *enum*

Contains result information.

 Field Name         |  Field Type            |  Card.  |  Description
:-------------------|:-----------------------|:--------|:------------
 resultCode         |  DirectResultCodeType  |  1      |  The machine-readable result code.
 resultDescription  |  string                |  1      |  The human-readable error description.


### DirectResultCodeType *enum*

Result and error codes for the class Result as return value for method calls.

 Value          |  Description
:---------------|:-------------
 ok             | Data accepted and processed.
 partly         | Not all control parameters could be applied.
 not-found      | Given EVSE-ID is not known to the operator.
 not-supported  | Given EVSE-ID does not support OCHP-direct.
 invalid-id     | The DirectId is not valid or has expired.
 server         | Internal server error.


### DirectId *class*

The session ID for one OCHP-direct charging process. The ID is created
by the operator and used to reference the session by the provider. Must
be unique per Operator-ID.
There are two events that create a new DirectId:

 * A successful call to SelectEvse by the provider
 * Local start of a charging session (i.e. via RFID) for a OCHP-direct
   enabled Contract-ID at a OCHP-direct enabled EVSE

```regex
[A-Z0-9\-]{1,255}
```


### DirectOperation *enum*

Operations to control an OCHP-direct charging process.

 Value   |  Description
:--------|:-------------
 start   | Initiate the start. Operator should allow the user to plug in.
 change  | Change the parameters of the charging process.
 end     | End the charging process. Operator should allow the user to plug out.


### DirectMessage *enum*

Messages to inform a provider about an OCHP-direct charging process.

 Value   |  Description
:--------|:-------------
 start   | A OCHP-direct charging process has been started.
 change  | The parameters of the charging process were changed.
 info    | A informative update is available, e.g. updated consumed energy value.
 end     | The charging process has ended, the connector was unplugged.
 finish  | The session is finished and the CDR was sent out.






# Binding to Transport Protocol


## OCHP direct over SOAP

For this protocol the SOAP Version 1.1 MUST be used.


