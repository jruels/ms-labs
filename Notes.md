# Class Notes
## Organization/Businesses
- Existing business - process re-engineering

## Different forms of development
- Traditional existing model
    - code management
    - build managment 
    - deployment mangement

## How we build software
- Infrastucture as code
- Two solitudes: 
    - DEV:  software world
    - OPS: hardware
- Virtualization
    - Automating the interactions with hardward
    - VMs use hyperviser
    - AWS and cloud services
    - Virtual environment is an illusion
- We write code to tell the operations environment what to do
    - We leave it up the environment to figure out
    - Docker and similar tech
- Terraform

## Data Challence 
- We have each MS has its own data - how do share and sychronize the data.
- If there is a shared data store, how do we access it and manage it?

## Strangler Options
- New features become MS
- Migrate to MS when a feature needs to be upgraded
- May be connections that we can't migrate to MS - non-modularized monolith
- Move a non-critical low use funtionality
    - Pilot - reduce the risk, try out our process.

### Couple with a cloud Migration
- hybrid cloud - new dev taking place a cloud based MS

---
 # October 4

 Specific areas we want to cover  
 - Big data 
    - Kafka
    - Data lakes 
    - AWS tour
    - Distriuted systems and consistency

## Issues involved in migrating to MS
- Code
    - Code design - API usage
    - 12 factor 
- Containerization
    - Docker - deployable container
    - Orchestration with Kubernetes
- Asynchronous MS
    - Kafka 
- Security

- Git management

- Complex back end data models - mapping to domain.

---

## Microservice
- Clients talk to MS in the language of the domain.
- MS talk to the infrastructure layer in the language of the domain
- Infrastructure layer converts requests to the underlying representation 
- MS should not dictate the design of data
- Infra layer suppies an interface for data access. 


## Backing service
- Data Service - is a infrastructure service that communicates with data repository
    - takes a data request - expressed as a domain object 
    - generates the appropriate query 
    - knows the data schema and structure 

## Progamming / congifuring

- Imperative
    - issuing step by step instruction
    - Fortram, Java
- Declarative
    - State the desired result
    - System figure out how to do it.
    - Functional programming

## Oct 6

- Schema strategies
- Review of kubnetes in AWS
- Streaming Microservices - event driven MS, asychronous MS - Kafka
- Data - MS interact 
- Supporting tools - Spring boot
- ETL -> ELT

### Drivers
- ENabling hardware
- Enabling software
- Market forces

https://www.redhat.com/architect/schema-registry

The Untied States  Chili Chile

## Inteceptor

- Post /order/  -> order microservier   "customer= {..}"

- Interceptor MS - receives fhe message - data transforms then passs it on

## Data
- Operational data - generated and used during operations 
    - Can remain in the context in its own contex
    - Data service backing store - interface into the infrastucture
- Corporate data assets
    - Data that is curated and managed organizastionally
    - Data backing to access that corporate data
- Historical data
    - stuff that does into ML and data analytics
    - big data environment 