---
layout: post
title:  Kotlin Flow & Channel - Part 1
date:   2022-12-12 10:05:55 +0700
image:   assets/images/blogs/kotlin_flow.webp
author: Man Ho
tags:   Android Development
---

## Table of contents
- [Example source code](#example-source-code)
- [Streams](#streams-hot---cold-streams)
    - [Cold streams](#cold-streams)
    - [Hot streams](#hot-streams)
- [Flow](#flow)
- [Conclusion](#conclusion)

## All parts of this article
* [Kotlin Flow & Channel - Part 1: Streams & Flow](/blogs/kotlin-flow-channel-part-1){:target="_blank"}
* [Kotlin Flow & Channel - Part 2: StateFlow & SharedFlow](/blogs/kotlin-flow-channel-part-2){:target="_blank"}
* [Kotlin Flow & Channel - Part 3: Channels & operators](/blogs/kotlin-flow-channel-part-3){:target="_blank"}

## Example source code
[GitHub](https://github.com/homanad/Flow-Channel){:target="_blank"}

## Streams/ Hot - Cold streams

Before we start with the flow and new things offered by Kotlin, let's recap a little bit about
Streams concept, types of streams and its properties.

### Streams

* There are two types of streams: **_Hot_** and **_Cold_** streams.
* To distinguish these two types of streams, we can rely on 3 main characteristics:
    * Where the data is produced: the data ca be produced inside or outside the stream. In other
      words, the data exists (or doesn't) no matter if you use the stream or not.
    * How many receivers at the same time can get the data: unicast or multicast mechanism.
    * Laziness: when the stream starts to emit values. Can it starts eagerly or it will start when
      we request for it.

#### Cold streams

* Data is produced inside the stream.
* Cold stream has only one subscriber and is initialized only when a subscriber starts listening to
  it (each subscriber listening generates a different cold stream) -> _**Unicast mechanism**_
* Each time a subscriber starts listening, the cold stream is reinitialized and the code inside it
  will be executed again, and has nothing to do with the previous execution -> Multiple instances of
  the same stream and are completely independent of each other.
* A lazy stream, only initialize and emit data when there is subscriber
* Sometimes, cold stream is not the same as defined, can emit different data depending on the
  listening time, like a hot stream inside a cold stream.

#### Hot Streams

* Data is produced outside the stream.
* Data can exist without a subscriber
* Can have no or more subscribers, emit data simultaneously to all subscriber -> _**Multicast
  mechanism.**_
* Subscriber doesn't initiate the stream, it's only called to start listening to the stream.
* Depending on when to start listening, subscribers may receive different data.
* A eagerly stream, always starts even without subscribers.

## Flow

* **_Flow is a cold stream_**, which means it only emits data when there is a subscriber.
* As the name, flow is like a flow, it only flows, not stores.
* Always execute the same code block when a subscriber starts listening (cold stream concept)
* Example:

    * This is my ViewModel with a flow:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/flow_viewmodel.png %}" />
      
    * And this my activity code:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/flow_activity1.png %}" />
        <br/>
      <img src="{% link assets/images/attachments/kotlin_flow_channel/flow_activity2.png %}" />

        * I will start Observer 1 right after my activity is created;
        * And after delaying 1s, I will start Observer 2;
        * And then, when I click **_Start observer 3_** button, it will start Observer 3.
    * Ok, let's see what happens!

      <img src="{% link assets/images/attachments/kotlin_flow_channel/flow.gif %}" width="250" />
    * As you can see, it doesn't matter when we start collecting data from the flow, the flow is
      always is recreated and executes the code block inside it, and emitting all the data.
    
## Conclusion

* It's about streams and Flow (cold stream), in the next article, we will talk about StateFlow and
SharedFlow to see the difference between them ;)
* Related articles: 
  * [Kotlin Flow & Channel - Part 2: StateFlow & SharedFlow](/blogs/kotlin-flow-channel-part-2){:target="_blank"}
  * [Kotlin Flow & Channel - Part 3: Channels & operators](/blogs/kotlin-flow-channel-part-3){:target="_blank"}