---
layout: post
title:  Kotlin Flow & Channel - Part 3
date:   2022-12-12 12:05:55 +0700
image:   assets/images/blogs/kotlin_flow.webp
author: Man Ho
tags:   Android Development
---

### Table of contents
- [Example source code](#example-source-code)
- [Channel](#channel)
    - [Channel types](#channel-types)
    - [Difference between SendChannel.close() and ReceiveChannel.cancel()](#difference-between-sendchannelclose-and-receivechannelcancel)
    - [Consume values](#consume-values)
- [Operators](#operators)
    - [Immediate operators](#immediate-operators)
    - [Terminal operators](#terminal-operators)
- [Conclusion](#conclusion)

### All parts of this article
* [Kotlin Flow & Channel - Part 1: Streams & Flow](/2022/12/12/kotlin-flow-channel-part-1/){:target="_blank"}
* [Kotlin Flow & Channel - Part 2: StateFlow & SharedFlow](/2022/12/12/kotlin-flow-channel-part-2/){:target="_blank"}
* [Kotlin Flow & Channel - Part 3: Channels & operators](/2022/12/12/kotlin-flow-channel-part-3/){:target="_blank"}

### Example source code
[GitHub](https://github.com/homanad/Flow-Channel){:target="_blank"}

### Channel

* **_Channel is a hot stream_**, it implements SendChannel and ReceiveChannel
* `SendChannel` provides two methods to emit data:

    * `trySend()`: add an element to the buffer immediately, and return ChannelResult:

        * `isSuccess`: it's `true` if the element is added successfully. n this case `isFailure` and
          `isClosed` return `false`.
        * `isFailed`: it's `true` if the buffer is full. In this case, `isSuccess` return `false`.
        * `isClosed`: it's `true` if the channel is **_closed or failed_** (we will talk about these
          two types later). In this case, `isSuccess` return `false`, `isFailed` return `true`
    * `send()`: If the buffer is not full, the element is added immediately, otherwise `send()` **_
      will be suspended and wait_** until the buffer is available.
    * Summary:

        * If we want to send data immediately without waiting, that is acceptable to ignore data if
          buffer is full, use `trySend()`
        * If we want to be sure that data has tobe sent, and can wait until the buffer is available
          to process it, should use `send()`
* In additional:

    * `close()`: close the channel immediately, then `isClosedForSend` will return `true`, call
      `close()` with a cause will make this channel a `Failed Channel`.
    * `isClosedForSend` (ExperimentalCoroutinesApi): returns `true` if the channel is closed by an
      invocation of `close()`
    * `invokeOnClose()` (ExperimentalCoroutinesApi): which is synchronously invoked once the channel
      is closed (`SendChannel.close()`) or the receiving side of this channel
      is `ReceiveChannel.cancel`.
* `ReceiveChannel` provides two methods to receive data:

    * `tryReceive()`: receive data synchronously, it return `ChannelResult` (same as `trySend()`)
    * `receive()`: The data will be received asynchronously, if the buffer has data, the data will
      be emitted, otherwise, the receive will be suspended and wait until the data is put into the
      buffer.
    * Summary:
        * If we want to get the data immediately at call time, accepting null value, we can
          use `tryReceive()`.
        * If we want to receive data asynchronously, and wait until there is data, we should use
          `receive()`
* In additional:

    * `isClosedForReceive`: determines whether the receiver is still available to receive data or
      not. For this value to be `true`, we need 2 conditions:

        * When somewhere calls `SendChannel.close()`
        * All items inside buffer have been received by receiver -> buffer is empty, if the buffer
          is not empty, the channel still cannot close for receive.
        * So, even when calling `SendChannel.close()`, but the buffer still has data, then the
          channel will not be closed until all the data in the buffer is emitted -> buffer is empty.
    * `ReceiveChannel.cancel()`: Used to stop emitting all data inside the buffer, and clear
      undelivered data (clean buffer).

#### Channel types

##### Rendezvous Channel

* The default capacity value when initializing a `Channel` is `RENDEZVOUS`, the value
  of `RENDEZVOUS`
  is 0, i.e capacity will be 0. We can say this channel doesn't exist buffer, which means items are
  only sent and received when the sender and receiver are met.
* Technically:

    * `send()` functions will be suspended until receiver appears.
    * `receive()` functions will be suspended until sender calls `send()`.
* Example:

    * This is my view model:
  
      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_rendezvous_viewmodel.png %}" />
  
    * And this is my activity code:
  
      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_rendezvous_activity.png %}" />

    * `rendezvousChannel` will emit a letter every 0.2s (I used `trySend()`), and we will start
      collecting that data when `button1` (**_Rendezvous channel_**) is clicked.
    * Ok, let's see what happens!
  
      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_rendezvous.gif %}" />
    
        * As you can see, since I use `trySend()`, and RENDEZVOUS has no buffer, so the previously
          emitted data will definitely be lost.
        * What if I use `send()` instead of `trySend()`? If you thought it would start emitting all
          the data, congratulations, you got it! Since the RENDEZVOUS channel has no buffer,
          the `send()` function will be suspended and wait for an Observer to start collecting data.
          Try changing the code yourself and see the difference between `send()` and `trySend()`!

##### Buffered Channel

* Instance of this channel is ArrayChannel. The value of `BUFFERED` is 64, that means buffer
  capacity will be 64, this will be the default value when initializing the channel as `BUFFERED`.
* Technically:
    * `send()` functions will be suspended when buffer is full (64 items inside).
    * `receive()` functions will be suspended when buffer is empty.
* Example:
    * This is my view model:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_buffered_viewmodel.png %}" />
    
    * And this is activity code:
  
      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_buffered_activity.png %}" />
  
    * `bufferedChannel` will emit a letter every 0.2s (I used `trySend()`), and we will start
      collecting that data when `button2` (**_Buffered channel_**) is clicked.
    * Ok, let's see what happens!

      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_buffered.gif %}" />

        * As you can see, even if I start collecting it after a few seconds, you still see a bunch
          of previous letters being emitted at the same time, then continue to collect each
          subsequent character.
        * That's the nature of the buffer, in this case it can store up to 64 letters, and almost
          infinite for **_UNLIMITED channel_**, so I'll skip the example for UNLIMITED channel.

##### Unlimited Channel

* Instance of this channel is LinkedListChannel. The value of `LIMITED` is `Int.MAX_VALUE`,
  basically we can assume it has unlimited buffer.
* Technically:
    * `send()` functions will never be suspended.
    * `receive()` will be suspended when buffer is empty.

##### Conflated Channel

* Instance of this channel is ConflatedChannel.
* As for how it works, it feels like it has an unlimited buffer (`send()` will never be suspended).
* However, its special thing is that it keeps only the last item, and skips the previous item if it
  hasn't yet received by any receiver (`DROP_OLDEST`). If it has only one item, and if that item
  received, buffer will be empty.
* Technically:

    * `send()` will never be suspended.
    * `receive()` will be suspended if buffer is empty.
* Conflated Channel is somewhat similar to **_Rendezvous channel and trySend()**_ (data not received
  will be lost), but also like Unlimited channel where send() never suspends, but it only holds one
  last item, try to pull my code and change it to Conflated channel for better understanding.

##### Buffered Channel with custom capacity

* It is similar to BUFFERED channel, the only difference is that we pass an optional capacity value
  into the constructor.
* In this section, I will try it in two case: with send() and trySend()

    * This is my view model with two custom capacity channels:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_custom_capacity_viewmodel.png %}" />
    
    * And this is my activity code:
  
      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_custom_capacity_activity.png %}" />
  
    * `customizedCapacityChannel1` will `trySend()` and `customizedCapacityChannel2` will `send()`
      each character every 0.2s, and we start collecting them for `button3` and `button4`
      respectively, let's see what happens for each channel:

      <img src="{% link assets/images/attachments/kotlin_flow_channel/channel_custom_capacity.gif %}" />
    
        * To explain about `chars`, it is a string and I use `split` function, so there will always
          be an empty word at the beginning and end of this list.
          `private val chars = "Hello World! These are Channels example!!!".split("")`
        * As you can see:

            * `customizedCapacityChannel1`: I use `trySend()`, so after buffer is full (we have 5
              letters -> "Hell" and the first one is empty), then I press `button3` (
              **_Custom capacity 1_**), we will immediately get "Hell" from Channel and items are
              being emitted -> non-consecutive string.
            * `customizedCapacityChannel2`: I use `send()`, so when buffer is full, `send` function
              will suspend and wait until I press `button4` (**_Custom capacity 2_**), after data
              inside Channel has been received, `send()` will continue to run,
              then `customizedCapacityChannel2` will continue to emit data -> consecutive series.

#### Difference between SendChannel.close() and ReceiveChannel.cancel():

| SendChannel.close()                                                                          | ReceiveChannel.cancel()                                    |
| ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Pending items inside the buffer will not be deleted                                          | Pending items inside the buffer will be deleted            |
| ReceiveChannel will still receive data if there are still items in the buffer                | Receive will no longer receive any items                   |
| ReceiveChannel.isClosedForReceive will not be true if there are still items inside in buffer | ReceiveChannel.isClosedForReceive will immediately be true |

* **_Closed Channel_** và **_Failed Channel_**:

    * The `close()` method inside SendChannel can throw an exception, if we pass an exception, it
      will be treated as a `FailedChannel`
    * Closed channel:

        * Call `SendChannel.close()` without passing exception.
        * When trying to call `send()` -> ClosedSendChannelException will be thrown.
        * When calling `tryReceive()` from `ReceiveChannel`, `isClosed` return true
        * When calling `receive()`, it throws `ClosedReceiveChannelException`
    * Failed Channel:

        * Call `SendChannel.close()` with passing exception.
        * When calling `send()`, it throws that exception.
* There are some examples for closed and failed channels in my source code, so please try it out to
  see what happens!

#### Consume values

* As mentioned earlier, we can receive data from a channel using `receive()` or `tryReceive()`, but
  these functions return only one value, so to process more than 1 value we can use `flow`, in here
  we can get all values from `Channel` with just one `terminal operator` (`consumeAsFlow()`
  , `consumeEach()`,...)

### Operators

#### Immediate operators

* Are operators that will not execute against the flow, instead, operators only execute something
  with the data returned from the flow, and return something else.
* -> Kind of understandable as in a flowing water, the flow will pass through a plant, that plant
  treats the water and continues transmit treated water.
* However, some operators will not return data, it only adds methods or functions to flow, which can
  be mentioned as `takeWhile`, `dropWhile`,...
* Immediate operators are not suspendable functions, but can work with suspend functions inside,
  which means we can create a sequence of operations.

#### Terminal operators

* These are suspendable functions and the purpose is to collect values from the upstream flow.
* These are also the operators that will initialize the flow, if no operator is called, the flow
  will not start data transmitter (same as Rx subscription)
* As the name suggests, after calling the terminal, you won't be able to apply any other operators
* Several terminals: `collect`, `single`, `toList`,...

### Conclusion

Those are the basics when we start moving from Rx and LiveData to Kotlin coroutines and flows, in
the next article, I will go through and explain common operators or will be used in some specific
cases. It will be soon!

* Related articles: 
  * [Kotlin Flow & Channel - Part 1: Streams & Flow](/blogs/kotlin-flow-channel-part-1){:target="_blank"}
  * [Kotlin Flow & Channel - Part 2: StateFlow & SharedFlow](/blogs/kotlin-flow-channel-part-2){:target="_blank"}