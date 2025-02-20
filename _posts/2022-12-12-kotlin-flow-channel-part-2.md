---
layout: post
title:  Kotlin Flow & Channel - Part 2
description: StateFlow & SharedFlow
date:   2022-12-12 11:05:55 +0700
image:   assets/images/blogs/kotlin_flow.webp
author: Man Ho
tags:   Android Development
github_url:  https://github.com/homanad/Flow-Channel
---

### Table of contents
- [Example source code](#example-source-code)
- [StateFlow](#stateflow)
- [SharedFlow](#sharedflow)
- [Conclusion](#conclusion)

### All parts of this article
* [Kotlin Flow & Channel - Part 1: Streams & Flow](/2022/12/12/kotlin-flow-channel-part-1/){:target="_blank"}
* [Kotlin Flow & Channel - Part 2: StateFlow & SharedFlow](/2022/12/12/kotlin-flow-channel-part-2/){:target="_blank"}
* [Kotlin Flow & Channel - Part 3: Channels & operators](/2022/12/12/kotlin-flow-channel-part-3/){:target="_blank"}

### Example source code
[GitHub](https://github.com/homanad/Flow-Channel){:target="_blank"}

### StateFlow

* **_StateFlow is a hot stream_**, holds and emits data to subscribers, and keeps data even without
  subscriber.
* When a subscriber starts collecting StateFlow, it receives the last data and subsequent data.
* StateFlow is quite similar to LiveData, the differences:

    * Always have initial data (non-null)
    * LiveData automatically stops emitting data when subscriber is no longer active (`onPause` and
      after that), StateFlow can't be automatic (refer to `Lifecycle.repeatOnLifeCycle`)
* Turn a cold stream (Flow) into a hot stream (StateFlow) using `stateIn` operator.
* Example:

    * This is my ViewModel with a flow:
  
      <img src="{% link assets/images/attachments/kotlin_flow_channel/state_flow_viewmodel.png %}" />
  
    * And this my activity code:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/state_flow_activity1.png %}" />
      <br/>
      <img src="{% link assets/images/attachments/kotlin_flow_channel/state_flow_activity2.png %}" />

        * I will start Observer 1 right after my activity is created;
        * And after delaying 1,5s, I will start Observer 2;
        * And then, when I click **_Start observer 3_** button, it will start Observer 3.
    * Ok, let's see what happens!

      <img src="{% link assets/images/attachments/kotlin_flow_channel/state_flow.gif %}" />

    * As you can see, depending on when to start observing, the data received by each observer will
      be different. However, a special thing that we need to note, **_StateFlow will not emit 2
      duplicate data consecutively_**, we have lost 1 `l` in `Hello` and two `!` final.

        * Observer 1: it gets all the letters because it starts collecting from the beginning.
        * Observer 2: StateFlow emits each character in turn every 0.2s, ie after 1.4s there will be
          8 letters emitted (at "W" since we have an empty item at first position of `chars`),
          Observer 2 starts listening from 1.5s, so then we will get the last data before, which is
          the letter "W" and the letters after that.
        * Observer 3: of course, depending on the time we press the button, it will start
          collecting.

### SharedFlow

* **_SharedFlow is also a hot stream_**, a more customizable version than StateFlow.

    * `reply`: the number of values that will be sent to a new subscriber (cannot be negative,
      default is 0).
    * `extraBufferCapacity`: The number of values that will be buffered in the replay, emit will not
      suspend if the buffer is still empty.
    * `onBufferOverflow`: config an emit action if the buffer is full, by default emit will be
      suspended until the buffer is available (SUSPEND), there are also DROP_OLDEST and DROP_LATEST.
* Turn a cold stream (Flow) into a hot stream (SharedFlow) using `shareIn` operator.
* Example:

    * This is my view model:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/shared_flow_viewmodel.png %}" />

      * And this is my activity code:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/shared_flow_activity1.png %}" />
      <br/>
      <img src="{% link assets/images/attachments/kotlin_flow_channel/shared_flow_activity2.png %}" />

    * SharedFlow will emit one letter every 0.2s, Observer 1 will be started after 1.3s, Observer 2
      will be started after 3s, and Observer 3 will be started when I click `buttonStartObserver3`
      . So, let's see what happens!

      <img src="{% link assets/images/attachments/kotlin_flow_channel/shared_flow.gif %}" />

        * As you can see,
            * Observer 1: after 1.3s, there are 7 emitted letters, it means when we start
              collecting, it has to start printing from "W", but it can also print 3 letters
              before "W" as "lo ", that's because I defined `replay = 3`
            * Observer 2: Same as Observer 1, it can also replay 3 letters before I start collecting
              it.
            * Observer 3: of course, depending on the time we press the button, it will start
              collecting, and also replay 3 letters before that.

### Conclusion

* It's about StateFlow and SharedFlow (hot streams), in the next article, we will talk about
  Channel to see the difference between them ;)
* Related articles: 
  * [Kotlin Flow & Channel - Part 1: Streams & Flow](/blogs/kotlin-flow-channel-part-1){:target="_blank"}
  * [Kotlin Flow & Channel - Part 3: Channels & operators](/blogs/kotlin-flow-channel-part-3){:target="_blank"}