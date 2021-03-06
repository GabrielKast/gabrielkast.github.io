---
layout: post
category : conferences
tagline: "Deep dive into Kubernetes and other technologies"
tags : [devoxx]
---

##  Effective  Service API Design by Elliotte Rusty Harold (Google)
Works at Google Analytics

I was late and did not saw the beginning.
I think he mainly said : Remove apis are very different from local APIs designs
First most important advice.

### Design implementation

** Start with Documentation **
2nd most important advice in that talk
Start with the design
Focus on the raw HTTP : URL, paths, query strings, response format...
Think of client needs
Not schemas
Don't try to autogenerate an API from existing code, with few annotations , mapping methods to URLs...
Think about the query the client is goint to make and how to include it in the query, the URL. If needed, if more complicated, use JSON. If very complicated : XML
Not like a traditional library where you write your tests code first.
More useful, and safer

#### What to put in the API
* Start small and grow
* Consider read-only as a first step. One table then an other etc. Then from read-only to read-write. But always IF NEEDED
* YAGNI
  - Eeasier to add than to remove
  - much cheaper to add neglected feature than (remove) 10 unused featrures
Get the little as you can out there as fast as you can 


#### Design consideration
* single request single response with no side effect or ordering
* aim to idempotency
* noun oriented (= resource oriented as people say)
  - URL as thing/part of thing
  
* Standards verbs only : GET PUT DELETE POST
* Custom verbs should be unnecessary

#### Define your Endpoints (URLs)
* Be liberal in your use of URL paths. Avoid a single endpoint that address by parameter : /callAPI /soap /rest is easier to scale
* Each resource has a URL that independently identifies 

#### Data format
* JSON, XML, occasionnaly binaries if needed
* All UTF-8
* Storage format is not exchange format
  - protobuf internally , xml outside
* Data formats should rule things in , not rule them out. Allow to future extensibility
  
* Schemas are docuemntation not part of your processing chain
* Allow optionnal data : to all fields possible. 15 years to GOogle to learn that.
Everything is a string. If date, dateformat (ISO)
Numbers are trickier. Integer and be carefull of the range of the INT. Say what you will send. (the range). Decimal != floating (scientific)
Don't use the word Double or Float, just decimals

#### Define your trust level
What you promise people
* SLA
* deprecation schedule (more important). 2 years before deprecation is good
How much can developper rely on it
* Cost change, how it can change
* Plan for versioning. Easier without Schemas anf with Optional Fields
* Can someone else implement your API? Client lock-in . You're not lock-in but our product is better.

#### Client librarires
* Always nice to provide examples of how it works
* Useful , not required. Never assume clients will only use your library
  - Important, was an error with Google Analytics. Thought to use only java, c++.. What about perl, go etc...
  - It's a second efort
* Cannot hide the fundamental unreliability of a network library
* Hand-crafted is better than auto-generated. With some help, with some tools but write it by hand

#### Decouple API from the client
*  Do not assume only the client you wrote will consume your API
* Not even if you don't publish the API
* The ser should not assume anything about the client

#### Authentification and Authorization
Very difficult!!
* Wuthn = who are you?
* Authz : what can you do ?
* Neither is which resource you want? Resource ID belongs in the URL, not the auth. Most common issue.
Never define by the authz but by the URL
True and important on the web. Super important on an API

#### Authentification Schemes
* Basic over HTTPs still works if you don't need two-factor
* Consider piggybacking on existing logins : google, facebook...
* Three-legged oAuth, OpenID, JWT. Try not to invent thing, it's complex. Reuse
* Do not ask got more permissions than you need
* Leverage existing flows in the browser. Do not try to roll your own UX for this
* Provide for service and robot account. Think about it. What credentials do you have. Depends on the stack you use

#### Developer keys
* identifies the app not the user (!=authn)
* Not really a solved problem, trivially sleatable
* Helps identifies developers with good intentions. Does not stop bad actors
* Another bump in the road for API adoption

#### Performance
* Not as important as on a web page since a program doesn't get bored :)
* use HTTP tricks: keep-alive , gzip, http2, etc...
* Paginate
* For 10 seons consider go async


#### Rate limiting
* HTTP status 429 (could be 403 or 503 as well)
* Stringly prefer by user , if at all possible
* Use developer key if you must, but it's almost trivially forgeable

#### Specs & Tools
* Swagger and open APIs are worth a look if JSON you well. Not so much for xml
* Easier/cleaner to design URL structure outide of Swagger and describe it after, in swagger

#### Framwworks
* Google cloude endpoint
* nodejs , Apache Jersey, Azure API... Sprin

### Final thoughts
* Remote APIs are differen 
* Strick to standard : hTTP
* Work with the stack not against it
* Start small and grow
* interface first, then the implementation





## Distributed commit logs with Kafka by James Ward
@_jamesward

We are moving from a machine to distributed programming

### Really bad analogy time
RAID5 was a game changer
Kafka is the RAID5 for event streams
  - Event streams
  - Distributed
  - Redundent
  
### Kafka fundamentals
* Messaging system semantics. Same terminology for Streaming and messaging
* Clustering is core
* Durability and ordering guarantees

### Use cases
More and more used.
* Modern ETL and Change Data Capture. Processing csv, etc... Capture all changes in a system
* Data pipelines
* Big Data ingest. An endpoint for Big Data. Some ingest data, other services subscribe. Can be a great buffer for ingested data.
(words : producers, stream processors, connectoirs, consumers)


### The vernacular of Kafkas : Vocabulary

#### Records
the containers that contains data:
- name, value, timestamp
- immutable
- append only
- Persisted on the Cluster and on disk (often one week window, but most of the time it's thrown after)
Known as a LOG on Kafka
Kafka is distributed commit log in that sense

##### Producers ans consumers
* Broker : one node in the cluster
* Producer: writes Records to a broker
* consumer reads Records from a broker
Kafka does not do push
The broker reads. It's not Kafka that push
* Cluser distribution :  leader/follower

#### Topics & partitions
There is messaging, topics etc.. as in messaging systems
* Topic:  Logical name for 1 or more partition(s)
  - You devine a topic and write it on different partitions. analogy with raid5
* Partitions are replicated (with leader/follower , election if node goes down etc..) Simple interface to write
* Ordering is gauranteed for a partition . Not in a toppic but in a partition :)
If you work on one partition, you work with only one node in fact (even if replcaited). 
Partition gives the horizontal scale

#### Offsets
Every message has an offset value. An ID
Unique sequential ID per partition. Unique across the partition
Benefits : 
  - Replay : Kafka is the buffer for you, you can replay easily
  - Different speed consumers : Canssandra is faster han postgres etc... The consumer does what he wants
  
#### Consumer groups
Logical name for 1/x consumers
A message Delivered once to a to one consumer of the consumer group
Message is load balanced in the Consumed Group

### DEMO!!
You can subscribe/unsubscrbe and begin to read at your rythm...



### CLIENTS
* JVM is officiel
* Most other platforms via the communauty
* Polling based

Before to dive into Kafka..
#### Akka streams
Implementation of reactive streams
* The "Source and sink paradigm". Source/sink strea programming
* deals with back-pressure, etc.
* There is a great kafka adapter for Kafka in Akka. Akka

#### Example with Akka-stream and Kafka
trait kafka {
  sink
  source
}

* have a serializer for key, value (sink)
* Have a config : akka.kafka.producer settings
* To produce a sink : Producer.plainSink(producerSettings)
* To send to K : tickSource = Source.tick(Duration.zero, 500.milliseconds, Unit).map(_ => Random.nextInt().toString).
Tick event of the source, will send data to Kafka
tickSource.map(new ProducerRecord[String, String,])("RandomBombers", _)).to (akfka.sink).run()(app.materializer)
source is producer record
sink is ...
How do you do your sharding : Decide on the data, on how ot's called... Many methods


The Materializer : for akka, tell akka stream how to run that flow on top of Akka

#### Consumer side
a websocket that consumes things
// "randomNumers" : name of the topic
val source = kafka.source("randomNumers", 

def source(topic:String, maybeOffset:Option[long]) : Source[ConsumerRecord[String, String]] = {
    // If there is a offset I use it 
    Subscriptions.assignmentWithOffset(New TopicPartition(topic, 0), offset)
    // Then
    Consumer.plainSource(consumerSettings, subscirptions)
}

The consumer:
source = kafka.source("randomNumers", maybeOffset).map{
  consumerRecord => Json.obj(consumerRecord.offset().toString -> consumer.value())
}

And in the javascript, you get data from the websocket

See. The code on github

Q/A:
performance : great!

Kafka/Reddis.
 - Reddis is mostly for durable data.
 - Clustering is not the first concern




## A crash course in Hardware

The Von Neumann Architecture


WAYY too much things to say about that amazing talk by Cliff Click. Ludovic (Terracotta) otld me that he is the real dad of Hotspot and JIT.
His talk was a must see
He explained that the most costing thing in our programs is cache miss. And going multicore is ot easy at all as you have to deal with (shared) cache on multicore





## Debugging Distributed Systems
by Donny Nadolny
PagerDuty
About ZooKeeper


Zookeeper at pagerduty
ZK to keep a lock on a world wide network
The failure PagerDuty had : Network trouble, one follower falls behind. Then ZK gets stuck
On a leader/follower normally you have to check if the leader has >= values than the followers.
Recovery : restart leader and it works

First hint : "Too busy to snap, skiping"
ZK is keeping a snapshot of ...

To test : mount one disk:
sshfs donny@othermachine /mount

#### Health checks
* First warning : application monitoring
* High level application checks are good, catch problems but do not tell the cause
* ZK monitoring : used ruok (are you ok?)
ZK said I'm OK at all the nodes but the cluster did not progress

#### Deep health checks
I leads to a log (?!) with a stacktrace

Deadlock
Why ZK heartbeat didn't elect another leader?
But the Quorumm Peer still say : no problem, A tick is sent to say no pb


TCP behaviour, The TCP data transmmission
You try 15 times to see if you can connect to another machine.
By default on Linux => 15.5 minuts!
TCP Data Retransmission, 

Timeline
* Network problem
* The leader try to close the connection
* Network comes back again
* hanging

On one side the Follower is trying to conect to Leader : 1min 40
On the other side the Follower is trying to conect to Leader : 15.5 min

But th point iptables DROPped the long connection 15.5 min
Why ?
iptables -A input -mstate ESTABLISHED, RELATED -j ACCEPT
iptables has its own way to know the connetions
netstat != iptable connections, yes!!
iptables (or kernel?) : the COnnection closed is set on a timeout

### The full Story
* Packet loss
* Follower falls behind, requests snapshot
*
*

### So you can reproduce it
Follwower falls behind
tc qdisc # command line to add latency :))
(or just wait!)

tc qdisc del dev eth0 root # removebandwith restriction

All that was TCP related


### IPSec
encrypt all traffic (TCP, UDP...) in your machine(s) instead of stunnel
Sent with UDP package (yes , it works)
All is wrapped in UDP
With IPSec if some packets are lost, you must begin the IPSec conection again. So it goes from 60% to 100% loss

Lessons:
##### Lesson 1
* Hard
* Don't lock and block
* TCP can block for a really long time
* interfaces / abstract methods make analysis harder

##### Lesson 2
Would have been good to have automatic debug info collection : stack trace, heap dump, transaction logs, etc...

##### Lesson 3
* Applcation/dependency checks should be deep health checks
* Leader/follower heartbeats should be  deep health checks





## Elixir is awesome

by @koenighotze

#### Exlixir is developer focused
- Docuemntation
- Tools to build
- etc.

#### Erlang/OTP
- Beam VM
- Tools
- Some libraries
- Patterns for supervision


#### Elixir take all Erlang and makes a nice colourfull cake

### DEMO
mix new --sup --pizza_demo

### Pillars of Resilience and Reliability
- Message massing between isolated process
- Automatic recovering and monitoring
- Transparent distribution

### Supertools : gen_server et supervisor
#### gen_server
Light weight processes
Actors with a mailbox, computation and a State

#### supervisor

By the way I learn
observer:start().
observer:stop().


### Macros
### Protocols
### Ecto and Phoenix

### CRDTs

### Should we use all with Elixir?
Elixir all the things

Come for OTP , stay for... 
- New insights and iseas
- Clean patterns as part of the core
- Architecture and Tooling for IoT
- Community



## Containers by @arungupta
#### Persistent Volume
1- Provision Storage
2 - Claim
3-
### pet set
Stateful pods
Petset has 0..n-1 pets
pet has a deterministic name and a unique identity
 * stable hostname
 * ordinal index
Each Pet has at most one pod
Pet Set has at most... ???



## MESOS
Open source cluster manager from UC Berkeley
* Provides resource isolation and sharing across distributed applications
* Run distributed systems on same pool of nodes
 - hadoop, spark, Jenkins, Couchbase...
* Cluster monitoring...

#### Architecture of Mesos
- Zookeper quorum
- One master / n slaves
 You can run Marathon, Spark, Hadoop ... on Mesos. It has executors on Mesos
 In that Marathon executor you will have tasks
 
#### DC/OS : Distributed C / OS
Logical compliment for Mesos  : ZK, Docker repo...
Includes UI...
