# About

Comparison between JEE application in Classic IT and private cloud from technology viewpoint.

# Technology architecture

![JEE Technology stack cloud vs classic](./0000-JEE-classic-vs-cloud-technology-architecture.svg)

[Source image](https://docs.google.com/presentation/d/1XKH50tbcgSswZFIBNtuN8qkmvSUdr5ooEhVdqnXcuQs/edit?usp=sharing)

# Notes

- JEE has been declared dead by Gartner. JEE app server features such as
  clustering are made obsolete by the container orchestrator. JakarataEE 8/MicroProfile
  enables JEE for the cloud. Build stateless disposable services according to
  [12factor.net](http://12factor.net) principles.
- Light-weight runtime with only JEE8/MP libraries needed. MicroProfile: Config,
  Health Check, Metrics, OpenAPI, JWT Propagation, Fault Tolerance, REST client. 
- Marathon/K8S: multiple deployment strategies for zero-downtime deployments. Elastic scaling
- DC/OS monitoring: single pane of glass for multiple clusters/clouds.
- CMDB: classic via agents for inventory scanning. Cloud:query against management APIs.
  Data integration problem instead of product selection? Beware of procurement of Eierlegende Wollmilchsau. 
- Observability in cloud: OpenMetrics for monitoring (Prometheus Stack),
  OpenTracing for tracing (Jaeger), EFD stack for logging. CloudEvents spec
  supports tracing of mixed sync/async flows. Constantly monitor and measure
  synthetic transactions. BPM is application function.
- Robust vs Anti-fragile: accept that outages do happen, make applications
  robust against it. Build self healing infrastructure. Use async events instead
  of sync messages. Focus on MTTR instead of MTBF and MTDB (Mean Time to Deflect
  Blame)
- CAP theorem, choose AP. No 2PC. Make applications eventually consistent.
  Patterns: Transactional Outbox, Saga, Aggregate, Event Sourcing. Needs Event Bus (Kafka)
  for implementing patterns and extendable architecture / Data Mesh. 
- Why not JMS?
  - Weblogic instead of container clustering, needs Oracle DB or NAS for persistence (both not cloud-native)
  - does not have self-healing shared-nothing architecture
  - API is standardized but on-the-wire format is proprietary and not compatible with other tech stacks
  - does not provide ordering of messages (desirable for receiving multiple versions of documents in order)
  - does not support replay for time travelling
  - does not support load-balancing (clients must know cluster IPs)
  - pushes to clients instead of pull (clients cannot be ephemeral and does not scale)
  - pub-sub not possible with ephemeral clients (subscribers to a topic must
    be registered before a message from a topic can be received).
    
# Vision on Platform services

- The Platform team provides value to other DevOps teams by developing and hosting services that require substantial effort
  and knowledge to develop and operate. This enables DevOps team to focus on delivering value to the Business.
- In classic IT, team responsibilities are often not aligned with technological boundaries. This makes it hard to define 
  service agreements and improve performance. Platform teams are often overloaded with change requests and 
  incidents, and lack resources for innovation.
- In the cloud, DevOps and Platform teams are equal tennants. A DevOps team provides services that are useful to the business.
  The Platform team is just another DevOps team that provides services other DevOps teams. By rigourous automation and
  self-service delivery of the Platform, time is made availabile for continuous service improvement and innovation.
- In classic IT, platform services are hosted on static and shared infrastructure. In the cloud, additional service delivery
  models are possible:
  1. BYO: developed, hosted and consumed by a DevOps team. 
     Service is fully customizable. 
     This model enables continuous innovation of the Platform.
     Use this model to experiment and introduce new technology.
     A useful BYO service may evolve into a new Platform Service.
  2. Fork-and-run: developed by the Platform team, hosted and consumed by a DevOps team. 
     Service is opinated and has limited customization options.
     This model enables continuous improvement of the platform via pull requests instead of change requests.
     This is useful for ephemeral services, such as a build server, or data masking service. 
  3. Shared service: developed and hosted by the Platform team, consumed by a DevOps team.
     Needed for stafeful services that operate on shared data. Examples of such services are Github, or an
     artifact repository.
- BYO vs Platform is a trade-off between flexibility and operational excellence.
- In classic IT, the Technology Domain Architecture is the result of a top down process, focussed on control instead 
  of enablement. Typically, innovation is slow and painful. In the cloud, the Platform can be seen as a codification 
  of the Technology Domain Architecture. New service models enable innovation and collaborative evolution of the
  architecture.
