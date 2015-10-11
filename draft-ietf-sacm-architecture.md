---
stand_alone: true
ipr: trust200902
docname: draft-ietf-sacm-architecture-latest
cat: info
pi:
  strict: 'yes'
  toc: 'yes'
  tocdepth: '4'
  symrefs: 'yes'
  sortrefs: 'yes'
  compact: 'yes'
  subcompact: 'no'
title: Secure Automation and Continuous Monitoring (SACM) Architecture
abbrev: SACM Architecture
area: Security
wg: SACM
kw: template
author:
- role: editor
  ins: N. Cam-Winget
  name: Nancy Cam-Winget
  org: Cisco Systems
  street: 3550 Cisco Way
  city: San Jose
  region: CA
  code: '95134'
  country: US
  email: ncamwing@cisco.com
- ins: L. Lorenzin
  name: Lisa Lorenzin
  org: Pulse Secure
  street: 2700 Zanker Rd, Suite 200
  city: San Jose
  region: CA
  code: '95134'
  country: US
  email: llorenzin@pulsesecure.net
- ins: I. McDonald
  name: Ira E McDonald
  org: High North Inc
  street: PO Box 221
  city: Grand Marais
  region: MI
  code: '49839'
  country: US
  email: blueroofmusic@gmail.com
- ins: A. Woland
  name: Aaron Woland
  org: Cisco Systems
  street: 1900 South Blvd. Suite 200
  city: Charlotte
  region: NC
  code: '28203'
  country: US
  email: loxx@cisco.com
normative:
  RFC2119: 
  I-D.ietf-sacm-use-cases: 
  I-D.ietf-sacm-requirements: 
  I-D.ietf-sacm-terminology: 
informative:
  RFC5209: 
  RFC3444: 

--- abstract

This document defines an architecture for standardization of interfaces,
protocols, and information
models related to security automation and continuous monitoring. It describes
the basic architecture, components, and interfaces defined to enable the
collection, acquisition,
and verification of Posture
and Posture Assessments.

--- middle

# Introduction {#intro}

Several data models and protocols (including - but not limited to - NEA,
TCG TNC, SCAP, SWIDs, XMPP, etc.) are in use today that allow different applications
to perform the collection, acquisition, and assessment of posture.  These
applications can vary from being focused on general system and security management
to specialized configuration, compliance, and control systems. With an existing
varied set of applications, there is a strong desire to standardize data
models, protocols, and interfaces to better allow for the automation of such
data processes.

This document addresses general and architectural requirements defined in {{I-D.ietf-sacm-requirements}}.
The architecture described enables standardized collection, acquisition,
and verification
of Posture and Posture Assessments.  This architecture includes the components
and interfaces that can be
used to better identify the Information Model and type(s) of transport protocols
needed for communication.

This document uses terminology defined in {{I-D.ietf-sacm-terminology}}.


# Problem Statement {#problem-statement}

Securing information and the systems that store, process, and transmit that
information is a
challenging task for organizations of all sizes, and many security practitioners
spend much of their
time on manual processes.  Administrators can’t get technology from disparate
sources to work together;
they need information to make decisions, but the information is not available.
Everyone is collecting
the same data, but storing it as different information.  Administrators therefore
need to collect data
and craft their own information, which may not be accurate or interoperable
because it’s customized by
each administrator, not shared.

Security automation and continuous monitoring require a large and broad set
of mission and business
processes; to make the most effective of use of technology, the same data
must support multiple processes.
The need for complex characterization and assessment necessitates components
and functions that
interoperate and can build off each other to enable far-ranging and/or deep-diving
analysis. SACM is standardizing an information model, data models, operations,
and transports that will allow for administrators to share with others and
to use data from others interoperably.


# Architectural Overview {#arch}

At a high level, the SACM architecture describes "Where" and "How" information
and assessment of posture may be collected, processed (e.g. normalization,
translation, aggregation, etc.), assessed, exchanged, and/or stored. This
section provides an architectural overview of 1.) the basic architectural
building blocks, which – in combination – constitute SACM components (the
entities, the "where"), and 2.) the relationships and interaction between
these building blocks on the data plane and control plane (communications
and flows between entities, the "how").

The SACM architecture provides the basic means to describe and compose SACM
components. Components enable the basic functionality in SACM, such as Endpoint
Attribute Collection or Target Endpoint Posture Assessment.

The role(s) a component plays in the SACM architecture are determined by
the function(s) that component instantiates. Three main component roles are
defined: a Consumer (C), a Provider (P), and a Controller (Cr) used to facilitate
some of the security functions such as authentication and authorization and
other metadata functions. See {{roles}} for details on roles.

In SACM, components are composed of functions, the modular building blocks
in the SACM architecture. The SACM architecture defines the purpose of these
functions. Attributes and operations used by component functions are described
in other SACM documents. See {{functions}} for details on component functions.

Functions use SACM interfaces for communications between components.  Interfaces
handle management and control functions (such as authentication, authorization,
registration, and discovery), and enable SACM components to share information
(via publication, query, and subscription). Three primary interfaces are
defined: an interface for management and control (A), an interface for data
communication between the controller and providers or consumers (B), and
an interface for data communication directly between a provider and a consumer
(C). See {{interfaces}} for details on interfaces.

{{simple-architectural-model}} illustrates the relationships between component roles and interfaces:


~~~~
                       +--------------------------------------+
                       | +--------------------------------------+
                       | | +--------------------------------------+
                       | | |                                      |
                       +-| |            Consumer (C)              |
                         +-|                                      |
                           +--------------------------------------+
                             /   \         /   \            /   \
                            /     \       /     \          /     \
                            -     -       -  d  -          -     -
                             || ||A        | a  |B          |   |C
                             || ||         | t  |           |   |
                            -     -       -  a  -           |   |
                            \     /       \     /           |   |
                             \   /         \   /            |   |
                          /|---------------------|\         |   |
                   /|----/                         \--------| d |--|\
                  /     /      Controller (Cr)      \ ctrl  | a |    \
                  \     \                           / plane | t |    /
                   \|----\                         /--------| a |--|/
                          \|---------------------|/         |   |
                             /   \         /   \            |   |
                            /     \       /     \           |   |
                            -     -       -  d  -           |   |
                             || ||A        | a |B           |   |C
                             || ||         | t |            |   |
                            -     -       -  a  -          -     -
                            \     /       \     /          \     /
                             \   /         \   /            \   /
                           +------------------------------------+
                           |                                    |-+
                           |            Provider (P)            | |
                           |                                    | |-+
                           +------------------------------------+ | |
                             +------------------------------------+ |
                               +------------------------------------+



~~~~
{: #simple-architectural-model title='Simple Architectural Model'}

## Component Roles {#roles}

An endpoint, as defined in {{I-D.ietf-sacm-terminology}}, can operate in two primary ways: as the target of an assessment, and/or
as a
functional component
of the SACM architecture that can instantiate one or more functions (see {{functions}}).
In the SACM architecture, individual endpoints may be a target endpoint,
a component, or both simultaneously.  An
endpoint acting as a component may perform one or more roles.
Components can take on the role(s) of Provider, Consumer, and/or Controller.

### Provider {#provider}

The Provider (P or Provider) is the component
that
contributes Posture Assessment Information and/or Guidance either spontaneously
or
in response to a request. A Provider can be a Posture Evaluator, Posture
Collector,
Data Store (see {{provider-consumer-types}}), or an application that has
aggregated Posture Assessment Information that can be shared.

The Provider implements the capabilities and functions that must be handled
to share or
provide Posture Assessment information.

A Provider may provide information spontaneously, or in response to a direct
request
from a Consumer. The information may be filtered or truncated to provide
a subset of the
requested information to honor the request. This truncation may be performed
based on the
Consumer’s request and/or the Provider’s ability to filter. The latter case
may be due
to security considerations (e.g. authorization restrictions due to domain
segregation,
privacy, etc.).

The Provider may only be able to share the Posture Assessment Information
using a
specific data model and protocol. It may use a standard data model and/or
protocol, a
non-standard data model and/or protocol, or any combination of standard and
non-standard
data models and protocols.  However, it must support either one or more standard
data models, or one or more standard protocols. It may also choose to advertise
its capabilities
through a
metadata abstraction within the data model itself, or through the use of
the registration
function of the Controller (see {{controller}}).

The Provider must be authorized to provide the Posture Assessment Information
and
further, be authorized to do so with specific data models and protocols and/or
for
specific consumers.


### Consumer {#requestor}

The Consumer (C or Consumer) is the component
that
requests or accepts Posture Assessment Information and/or Guidance. A Consumer
can be
a Posture Evaluator, Report Generator, Data Store (see {{data-plane-functions}}),
or an application that consumes Posture Assessment Information in order to
perform another function.

As described in Section 2.2 of the SACM Use Cases {{I-D.ietf-sacm-use-cases}},
several usage scenarios are posed with different application types requesting
posture assessment
information. Whether it is a configuration verification system; a checklist
verification system;
or a system for detecting posture deviations, compliance or vulnerabilities,
they all need to
acquire information about Posture Assessment. The architectural component
performing such
requests is a Consumer.

The Consumer implements the capabilities and functions that must be handled
in order to
enable a Posture Assessment Information Request. Requests can be either
for a
single posture attribute or a set of posture attributes; those attributes
can be the
raw information, or an evaluation result based upon that information.
The
Consumer may further choose to query for the information directly (one-time
query), or
to request for updates to be provided as the Posture Assessment Information
changes
(subscription). A request could be made directly to an explicitly identified
Provider,
but a Consumer may also desire to obtain the information without having to
know the
available Providers.

There may be instances where a Consumer may be requesting information from
various
Providers and, due to its policy or application requirements, may need to
be better
informed of the Providers and their capabilities. In those use cases, a Consumer
may
also request to discover the respective capabilities of those Providers using
the
discovery function of the Controller (see {{controller}}) or may request
metadata reflecting the capabilities of the Providers.

The Controller (described below) must authorize a Consumer to acquire the
information
it is requesting. The Consumer may also be subject to limits or constraints
on the
numbers, types, sizes, and rate of requests.


### Types of Providers and Controllers {#provider-consumer-types}

SACM Providers and Consumers can perform a variety of SACM-related tasks.
For example, a Collector can perform Collection tasks; an Evaluator can
perform Evaluation tasks.  A single Provider or Consumer may be able to perform
only one task, or multiple tasks. SACM defines the following types of Providers/Consumers:

#### Collector {#collector}

A collector consumes Guidance and/or other Posture Assessment Information;
it provides Posture Assessment Information. Collectors may be internal or
external.

##### Internal Collector {#internal-collector}

An internal collector is a collector that runs on the endpoint and collects
posture information locally.


##### External Collector {#external-collector}

An external collector is a collector that observes endpoints from outside.
These collectors may be configured and operated to manage assets for reasons
including, but not limited to, posture assessment. Collectors that are not
primarily intended to support posture assessment (e.g. intrusion detection
systems) may still provide information that speaks to endpoint posture (e.g.
behavioral information).

Examples:



* A RADIUS server, which collects information about which endpoints have logged
onto the network

* A network profiling system, which collects information by discovering and
classifying network nodes

* A Network Intrusion Detection System (NIDS) sensor, which collects information
about endpoint behavior by observing network traffic

* A vulnerability scanner, which collects information about endpoint configuration
by scanning endpoints

* A hypervisor, which collects information about endpoints running as virtual
guests in its host environment

* A management system that configures and installs software on the endpoint,
which collects information based on its provisioning activities



##### Collector Interactions With Target Endpoints {#interactions-with-endpoint}

TODO - examples of endpoint interactions with local internal collector (e.g.
NEA client), endpoint with remote internal collector (SNMP query), and external
collector (sensor)



#### Evaluator {#evaluator}

An evaluator consumes Posture Assessment Information, Evaluation Results,
and/or Guidance; it provides Evaluation Results. An evaluator may consume
endpoint attribute assertions, previous evaluations of posture attributes,
or previous reports of Evaluation Results.

TODO: update the terminology doc to reflect this definition

Example: a NEA posture validator {{RFC5209}}


#### Report Generator {#report-generator}

A report generator consumes Posture Assessment Information, Evaluation Results,
and/or Guidance; it provides reports. These reports are based on:



* Endpoint Attribute Assertions, including Evaluation Results

* Other Reports (e.g., a weekly report may be created from daily reports)


It may summarize data continually, as the data arrives. It also may summarize
data in response to an ad hoc query.


#### Data Store {#data-store}

A data store consumes any data; it provides any data.



### Controller {#controller}

The Controller (Cr or Controller) is a component defined to facilitate information
sharing and
to execute on security functions and overall SACM management and control
system functions.  SACM defines three types of Controller:



{: hangIndent='1'}
Broker:
:   Intermediary negotiating connection between Provider and Consumer. Implements
only control plane functions. A Controller
acting as a Broker:

  * Receives a request for information from a Consumer and instructs the Consumer
where and how
retrieve the requested information.

  * Receives a publication request from a Provider and instructs the Provider
where and how to
deliver the published information.



The information itself is neither distributed nor stored by the Controller.



{: hangIndent='1'}
Proxy:
:   Intermediary negotiating on behalf of a Consumer or Provider. Implements
both control and data plane functions. A Controller
acting
as a Proxy:

  * Receives a request for information from a Consumer, retrieves the information
from the
appropriate Providers, and provides the information to the Consumer.

  * Receives a publication request from a Provider, accepts the published information,
and
distributes it to appropriate consumers.



The information itself is distributed by, but not stored by, the Controller.



{: hangIndent='1'}
Repository:
:   Intermediary receiving and storing data from a Provider, and providing stored
data to a Consumer.  Implements both control and data plane functions. A
Controller acting as a Repository:

  * Receives a request for information from a Consumer, retrieves the information
from its data
stores, and provides the information to the Consumer.

  * Receives a publication request from a provider, stores the published information,
and
distributes it to appropriate Consumers.



The information itself is both handled by and stored by the Controller.

A single instantiation of a Controller may be a Broker, Proxy, or Repository,
or any combination thereof.

Through the use of a discovery mechanism, Consumers can have visibility into
the Providers
present, the type(s) of Posture Assessment Information available, and how
it can be requested.
Similarly, a Provider may need to publish what Posture Assessment Information
it can share and
how it can share it (e.g. protocol, filtering capabilities, etc.). Enabling
this visibility
through a Controller or through metadata publication also allows for the
distinct definition of
security considerations (e.g. authorized registration / publication of capabilities
by Providers)
beyond how a Provider may define its own capability.

Beyond the control and management functions for the SACM system, a Controller
may also provide
proxy or broker or repository (and possibly routing) services in the
data plane.  In the deployment
scenario where Providers do not assert the need to know their Consumers and/or
vice versa, the
Controller can thus provide the appropriate services to ensure the Posture
Assessment Information
is appropriately communicated from the Providers to the authorized Consumers.

The Controller, acting as a management control plane, helps define how to
manage an overall SACM
system that allows for Consumers to obtain the desired Posture Assessment
Information without the need
to distinctly know and establish one (Consumer) to many (Provider) connections.
Similarly, a Provider
may not need to distinctly know and establish one (Provider) to many (Consumer)
connections; e.g. the
Controller enables the means to allow a SACM system to support many to many
connections. Note that the
Controller also allows for the direct discovery and connection between a
Consumer and Provider.

As a SACM component, the Controller may be instantiated within a system or
device acting as a
Provider or a Consumer (or both), or as its own distinct Controller entity.
In a rich SACM environment,
it is feasible to instantiate a Controller that provides both the management
(and control) functions for
SACM as well as providing the data plane services for the actual data,
e.g. Posture Assessment Information flow. Note that Controllers may be implemented
to only provide control plane functions (broker), or both control plane functions
and data plane services (proxy or repository).




# Interfaces between Consumers, Providers, and Controllers {#interfaces}

A SACM interface is a transport carrying operations (e.g. publication via
a RESTful API).  As shown in {{simple-architectural-model}}, communication can proceed with
the following interfaces and expected functions and behaviors:



{: hangIndent='1'}
A:
:   interface “A” shown in {{simple-architectural-model}} handles the management and control functions that are needed to establish,
at minimum,
a secure communication between Consumers and Providers. The interface must
also handle
the functions to allow for the discovery and registration of the Providers
as well as
the ways in which Posture Assessment Information can be provided (or requested).


{: hangIndent='1'}
B:
:   interface “B” shown in {{simple-architectural-model}} enables Providers to share their Posture Assessment Information spontaneously;
similarly, it enables Consumers to request information without having to
know the
identities (or reachability) of all the Providers that can fulfill Consumers’
requests.


{: hangIndent='1'}
C:
:   interface “C” shown in {{simple-architectural-model}} illustrates the ability and desire for Consumers and Providers to be able
to
communicate directly when a Provider is sharing Posture Assessment Information
directly to a Consumer. The interface allows for the different data models
and
protocols to be used between a Consumer and a Provider with the expectation
that the
appropriate authentication and authorization mechanisms have been employed
to
establish a secure communication link between the Consumer and the Provider.
Typically, it is expected that the secure link establishment occurs as a
management or
control function through the abstracted Controller role (e.g. the Controller
could be
a broker or could be embedded in a Consumer or a Provider).


A variety of protocols, such as SNMP, NETCONF, NEA protocols {{RFC5209}}, and other similar interfaces,
may be used for collection of data from the target endpoints by the Posture
Information Provider.
Those interfaces are outside the scope of SACM.


# Component Functions {#functions}

SACM components are composed of a variety of functions, which may be instantiated
on a single endpoint or on separate standalone endpoints providing various
roles. An endpoint MUST implement one or more of these functions to be considered
a SACM component. A SACM solution offers a set of functions across a set
of SACM components.

The functions described here are the minimum set that is mandatory to implement
in a SACM solution. A SACM solution MAY implement additional functions.

## Control Plane Functions {#control-plane-functions}

Control plane functions represent various services offered by the Controller
to the Providers
and Consumers to facilitate sharing of information. Control plane functions
include, but are not limited to:



{: hangIndent='1'}
Authentication:
:   The authentication of Consumers and Providers
independent of the actual information-sharing communication channel. This
supports
use cases where:

  * Consumers may request information independent of knowing the identities of
the
Providers.

  * Providers may want to share the information without prior solicitation.



The architecture must account for an abstraction where a Controller may be
defined
to effect the authentication of the Consumers and Providers independent of
the
actual information-sharing communication channel.



{: hangIndent='1'}
Authorization:
:   The restriction of Posture Assessment Information sharing
between the Consumers and Providers. At minimum, a management function must
define
the necessary policies.


{: hangIndent='1'}
Identity Management:
:   Since Identity Management for authentication and
authorization policies is best performed via a centralized component, the
Controller
also facilitates this function.


The Controller needs to be able to identify the endpoints participating as
SACM components
and the roles that they play.  Similar to how access control may be effected
via Authentication,
Authorization, and Accounting Systems (e.g. AAA services), the same principle
is defined; as
AAA services depend on Identity Management services, the Controller will
need a similar function
and interface to Identity Management services.



{: hangIndent='1'}
Registration/Discovery:
:   The discovery of what Providers are available, what
information a Provider can share, and how it can be requested / communicated.
A discovery
mechanism is required to facilitate interaction with Providers that may have
different
Posture Assessment Information and potentially limited, or a rich set of,
ways in which
they can share the information.



## Data Plane Functions {#data-plane-functions}

Subscription

Publication

Query



# Component Capabilities {#capabilities}

TODO: add a discussion of "capability" as being able to talk a specific data
model, data operations, or SACM transport

TODO: data plane capabilities / control plane capabilities can be discovered
via querying the controller


# Example Illustration of Functions and Workflow {#example}

TODO: once the group reaches consensus on content for the previous sections,
revise all this text based upon the agreed-upon architecture


~~~~
                   +-------------------------------+
                  | +-------------------------------+
                  | |                               |
                  +-|        Controller (Cr)        |
                    +-------------------------------+
                       //   /            \   \\
                      //   /              \   \\
                   A //   /                \   \\ A
                    //   /                  \   \\
                   //   /  B             B   \   \\
                  //   /                      \   \\
 +------------------------+           +------------------------+
 | +----------------------+     A     | +------------------------+
 | |                      |===========| |                        |
 | |    Consumer (C)      |-----------| |      Provider (P)      |
 +-|                      |     C     +-|                        |
    +---------------------+             +------------------------+

~~~~
{: #communications-model title='Communications Model'}

SACM’s focus is on the automation of collection, verification and update
of system security configurations pertaining to endpoint assessment.  In
order to carry out these tasks, the architectural components shown in {{simple-architectural-model}} can be further refined as:



{: hangIndent='1'}
Providers:
:   a Provider may be dedicated to perform either the collection, aggregation
or evaluation of one or more posture attributes whose results can be conveyed
to a Consumer. In this example form of the
SACM architecture model, these are shown as Collection, Evaluation, and Results
Providers. Note that there may be posture attributes or posture assessment
information that articulates Guidance information which may or may not be
present in the architecture.


{: hangIndent='1'}
Consumers:
:   a Consumer may request or receive one or more posture attributes or posture
assessment information from a Provider for
their own use.  In this example form of the SACM architecture model, these
are shown as Collection, Evaluation, and Results Consumers.  Note that there
may be posture attributes or posture assessment information articulating
Guidance information which may or may not be present in the architecture
to be provided or consumed.


{: hangIndent='1'}
Data Stores:
:   a Data Store is both a Provider and a Consumer,  storing one or more posture
attributes or assessments for endpoints.  It should be understood that these
repositories interface directly to a Provider or Consumer (and Guidance)
but the interfaces used to interact between them is outside the scope of
SACM (e.g. no interface arrows are shown in the architecture).


{{example-flow}} illustrates an example flow for how Posture Assessment Information may flow.


~~~~
                                +-------------+
                                 |Evaluation   |
                +-------------+  |Guidance     +--+
                |Endpoint     |  |Function     |  |
        +-------+             |  +-------------+  |
        |       |             |                   |
        |       +-------+-----+             +-----v-------+
        | Collection    |                   |Evaluation   |
      +-> Function   +--+--------+          |Function     |
      | |            |Collection |    +-----------+   +----------+
      | +------------+Provider   |    |           |---|          |
      |              |           |    |Collection |   |Evaluation|
      |              |           |    |Consumer   |   |Provider  |
      |              +----+------+    +----^------+   +---+------+
     ++---------+         |                |              |
     |Collection|   +-----v------+     +---+--------+     |
     |Guidance  |   |            |     |Collection  |     |
     |Function  |   |Collection  |     |Provider    |     |
     |          |   |Consumer    |-----|            |     |
     +----------+   +------------+     +------------+     |
                               | Collection |             |
                               | Data Store |             |
                               +------------+             |
                                                          |
         +--------------+           +---------------+     |
         |Evaluation    |           |Evaluation     |     |
         |Results       |           |Consumer       <-----+
         |Provider      |-----------|               |
         +-----+--------+           +---------------+
               |     |Results Reporting|
               |     |Function         |
               |     +------------^----+
               |                  |
         +-----v--------+    +----+------+
         |Evaluation    |    |Reporting  |
         |Results       |    |Guidance   |
         |Consumer      |    |Data Store |
         +---+----------+    +-----------+ +-------------+
             |                             | Results     |
             +-----------------------------> Data Store  |
                                           |             |
                                           +-------------+



~~~~
{: #example-flow title='Example Posture Information Flow'}

TODO - add example of / more content around interactions with endpoint, possible
communications patterns


# Acknowledgements {#Acknowledgements}

The authors would like to thank Jim Bieda, Henk Birkholz, Jessica Fitzgerald-McKay,
Trevor Freeman, Adam Montville, and David Waltermire for participating in
architecture design discussions, reviewing, and contributing to this draft.


# IANA Considerations {#IANA}

This memo includes no request to IANA.


# Security Considerations {#Security}

The SACM architecture defines three main components that interface with each
other both for management and control (in the control plane) and for the
sharing of Posture Assessment Information.  Considerations for transitivity
of trust between a Provider and Consumer can be made if there is a well understood
trust between the Provider and the Controller and between the Consumer and
Controller.  The trust must include strong mutual authentication, at minimum,
between the Provider and Controller and between the Consumer and Controller.

To address potential Man-in-the-Middle (MitM) attacks, it is also strongly
recommended that the communications be secured to include replay protection
and message integrity (e.g. transport integrity and if required, data integrity).
Similarly, to avoid potential message disclosure (e.g. where privacy may
be needed), confidentiality should also be provided.

As the Controller provides the security functions for the SACM system, the
Controller should provide strong authorizations based on either or both business
and regulatory policies to ensure that only authorized Consumers and obtaining
Posture Assessment Information from authorized Providers. It is presumed
that once authenticated and authorized, the Provider, Controller or Consumer
is deemed trustworthy; though note that it is possible that the modules or
devices hosting the SACM components may be compromised as well (e.g. due
to malware or tampering); however, addressing that level of trustworthiness
is out of scope for SACM.

As the data models defined through the interfaces are transport agnostic,
the Posture Assessment Information data in the interfaces may leverage the
transport security properties as the interfaces are transported between the
Provider, Consumer and Controller.  However, there may be other devices,
modules or components in the path between the Provider, Consumer and Controller
that may observe the interfaces flowing through them.


--- back
