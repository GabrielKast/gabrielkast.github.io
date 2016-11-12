---
layout: post
category : conferences
tagline: "Deep dive into Kubernetes and other technologies"
tags : [devoxx]
---

## Docker patterns and anti patterns

#### Too easy?
docker build on each environment
fast cheap and not always the way to o
FROM ubuntu #latest version 
etc... You must specify the version
FROM ubuntu:14.04
Better : with the same base image
FROM ubuntu:ksqmlskmlqksm <- the hash of the image
What about dependencies? nodejs etc...

Be carefull to have same version on test, preprod etc...

Need to have the same binaries for each environment : testing, staging, prod


### Immutable and stable binaries
#### What is immutable server pattern 
Instead uf upgrades, you apply the service/new service of an immutable server.
Have an efficient pipeline

example : Application(jar), JDK and a base, nginx

### Pipeline requirements
* Jar tested in containers
* Framework know + upgraded with secuity patches
* ??

### Pipeline 1
* Build unit tests
* framework build
* integration build : laestst jar file + FWK

#### Pipeline fir JAR : build with Jenking
build , tes integration tests and promite it as "release"

#### Pipeline
##### Build base image
docker pull nginx, build jdk, tests...
Jenkins job : docker-framework
- Build the image
- push the image to a repository. Here the artifactory (by jfrog...)
Nothing put in release directory, add smthing in "realse" repo is a promotion of a 
Add a tag to the built image
- test the image:
  * Does nginx installed work?
  * Does the JDK installed, work?
  
##### Build test image
- Build a test image based from the Base image. Add jar file and the ui project, the frontend
  * Add the config you need (nginx..)
- run it, test it (automatically)

##### Promote test image to release
The artifactory has a REST api for that.
Add a tag : version of the release and "latest"
docker-framework


### Pipeline : Application delivery
1.1 Docker pull released fwk image => in a docker-prod image
1.2 Build the app => libs-dev (in a repo? in Jenkins I think)
2. Join the two : docker build fwk image + JAR and js. put it in the repo.
   docker build => in the repo as docker-dev
3. Test the application
4. Promote the image to released repo
5. When tested etc. Promote the image to the prod repo

### Pipeline To recap (more or less)
Buidl the app
Build fwk . Promote it
Build fwk + app. test. Promite it to test

### Do automated tests for the pipeline

### What if there is several projects?
- What if a security patch break the app? You can test/release with the no patched release.. At least for dev

### Full automation
### Immutable server pattern done that way
You know what you put in production


### Add pipelines in Jenkins
You can declare your pipeline in Jenkins
get the code, build things, promote etc....


### Conclusion :release fast or die! 
But don't loose control on what you release








## Reactive Streams principles applied in Akka Streams
Eric Loots, from Lightbend

- He likes Merano, Italy (Jardin botanique)

Agenda :
* Reactive streams, the story
* Reactive streams, the protocol
* Akka
* Akka streams : demo.
* Q/A

### Reactive Streams
What is a stream ?
A stream is a controlled flow of data.
Let the data flow at the right rate

#### Story
- #NET 3.5 2009, reactive extensions
- 2013  : Reactive programming
  * Playfwk with iteratees
  * Akka IO
  * RxJava
Need for backpressure
 * Play : pull backpressure
 * Akka : Akka-io NACK, low-level
 * RxJava : no backpressure, nice API

** Iteratees
Great but complicated API

###### October2013
Problems with streamings, back-pressure...
New term : ractive non-blocking asynchronious backpressure" :) ...
 * get async
 * never block
 * safe : back-threads pressured
 * purely local abstraction
 * Allow synchronious aimpl/
 * Be compatible with TCP because that protocol implements backpressure
 
"Reactive Streams" expret group
=> 2014-15 :  1.0 released
2015 included in JDK


#### JEP-266
Looks simple but if there is two react library that must work together, it's complicated
Geoals of reactive streams!
* implement backpressure 
* interop over libraries
Don't use reactive streams directly but higher libraries... like akka streams. (I'm amazed)

### Akka
Make building concurrent & distributed apps simple
Message driven apps

Akka has :
* actors
* clustering and remoting
* cluster/sharding
* akka persistence
* streams
* akka http (previous spray)
* Akka typed (experimental today)

#### The actor model
Receive messages. And react to it.
Can send other messages, create other actors etc....
A concurrency & distributed construct.
* Addressable
* location-transparent

### AKKA STREAMS
on top of actors
Asynchronous back pressured stream processing
Source => Flow => Sink ( the link between the three, the '=>' can be async)
Flow :  pipeline
Sink: does some action
Source : send messages


If pb, backpressure : the Sink communicate to the Source to say : slow down :)
Ot's transparent

Not only linear streams but also feedbacks
Source1  ===> Flow  <==\\
               ||      |\
               \/      ||
Source2 ===> Flow ==> Flow

##### What is a materialization?
Gives a schema, a graph.
Can be optimzed - internal representations
Fusing & optimization. Then run()

### DEMO
* scala 2.12 (with java 8)
* Akka 2.4.12
An Audio echo generator using akka streams
Generate echo and cancel it


The schema of the Echo generator
There is an input. You echo it. 
bla => blaa => blaaa => blaaaa

ok it was a demo...


## Building chat bots : UI next gen
by James Ward 

### Why bots?
Because ideas popup in my mind
Too many apps
We use slack, facebook managers, chat channels all day long
* Use bots as the new apps
* use Natural language
** Answer person without changing context

AI makes the difference
* Natural language processing
* image recognotion
* Concept inference
* Sentimet analysis

### Bot architecture
* Messaging app  (slack, FB...)
* talks to a Service lienked to
  - Provider(s), backends...
  - A formatter...
  - AI

#### Bot protocol
* Machin to machine over http
  - mostly http and json
* All different & custom
* Some nodejs libraries to help (?)

Example : dream-house web app on heroku. 
dream-house-app.io

### First bot : SLACK
Different ways :
* Service == talk/post to a ==> channel
  The bot publish to the channle
* Service <== discuss in a  ==> channel
  You can interact with the bot, ask the Bot something. eg : price of house in Boston
* Direct conversation with the bot
  Begins a conversation. Can open a case in salesforce for instance :)

The code in 
github.com/dreamhouseapp/ ...

##### The code
in nodejs
You can do 
controlers.hears('help') => then display ..
controlers.hears("find (.*)") => then search...
controlers.hears("create case") => begin of a discussion...

Using botkit library

You can add style to the responses. 
Most of the time with bots, you send JSON and the bots style it. With/without style sent as properties. Slack display the JSON as he wants

Need authentification, steps at the beginning...

### Messenger Bot by Facebook
Architecture:
Messenger <= post => Bot
You can do it on messenger.com
You create a gave on facebook, your business page
You go on that page 
Then you can begin conversation
* help
* find ... 

#### Code on github too

At the moment there was no library. Done by hand.
- There is a dictionnary of request. Could be AI
- Controller
- Formatter to parse the data, add images...
You have some informations about the user in the Header

You can transition from bot to Human


### ALexa bot
Alexa <== post ==> Service
A device on AMAZON that listen to you and can answer :)

There is a declarative side in the App you add to alexa/amazon
You declare intents, the interaction
You add sample utterances, things that you will be talking about

You configure the endpoint. It can run on heroku, something else... and the AWS lambda

Your service must comes back in JSON with tags like <speak></speak>

Code on github dreamhouse...
nodejs and a module called "alexa"











