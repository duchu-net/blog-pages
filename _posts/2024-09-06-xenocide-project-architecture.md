---
layout: post
title: "Xenocide Project architecture"
categories: xenocide
author: Patryk Androsiuk
date: 2024-09-06
---

Hello World!

<img src="/blog-pages/assets/2024-09-06-xenocide-project-architecture/architecture-diagram.png" alt="Xenocide project architecture diagram"/>


```uml
@startuml

!theme bluegray
skinparam backgroundColor transparent

title Xenocide.online Architecture

cloud "Internet" as web
package "Front End" as fe {
  actor user
  interface "client" as clientInstance
  interface "landing" as landingInstance
}
rectangle "Internal Network" <<docker>> {
  node "Load balancer" <<nginx>> as balancer
  rectangle "Client app" <<react static>> as client
  node "Landing app" <<react nextjs>> as landing
  node "Gateway app" <<nestjs>> as api
  queue "Event bus" <<rabbitmq>> as bus
  package "Simulation Cluster" <<nestjs>> {
    node "Primary app" as sMaster
    collections "Worker app (1,...,n)" as sWorker
  }
  database "xeno-world" <<mongodb>> as dbworld
  database "xeno-user" <<mongodb>> as dbuser
}

web <-> balancer: "xenocide.online" <<Argo Tunnel>> 
clientInstance -down-> web
landingInstance -down-> web

balancer -down-> landing: "/index"
balancer -down-> client: "/client"
balancer -> api: "/api" <<http>>
balancer <-> api: "/socket.io" <<ws>>

api -> dbuser
api -> dbworld: <<read only>>
api <-down-> bus

bus <-down-> sMaster
bus <-> sWorker

sMaster <-> sWorker: <<process>>
sMaster -up-> dbworld
sWorker -up-> dbworld

@enduml
```
