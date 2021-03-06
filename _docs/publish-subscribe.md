# Solace OpenMAMA Publish Subscribe

* [Assumptions](#assumptions)
* [Goals](#goals)
* [Installation](#installation)
* [Getting Access to Solace Message Router](#getting-access-to-solace-message-router)
* [Solace Message Router Properties](#solace-message-router-properties)
* [Trying It Yourself](#trying-it-yourself)
* [Receiving Message](#receiving-message)
* [Sending Message](#sending-message)
* [Summarizing](#summarizing)

---

## Assumptions

This tutorial assumes the following:

*   You are familiar with OpenMAMA [core concepts](https://sftp.solacesystems.com/Portal_Docs/OpenMAMA_User_Guide/01_Introduction.html).
    *   If not, see [this OpenMAMA guide](http://www.openmama.org/content/quick-start-guide) and [Solace OpenMAMA "Hello World" tutorial](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md).
*   You are familiar with Solace [core concepts](http://dev.solacesystems.com/docs/core-concepts/).
*   You have access to a properly installed OpenMAMA [release](https://github.com/OpenMAMA/OpenMAMA/releases).
    *   Solace middleware bridge with its dependencies is also installed
*   You have access to a running Solace message router with the following configuration:
    *   Enabled Message VPN
    *   Enabled Client Username

---

## Goals

The goal of this tutorial is to demonstrate basic messaging interactions using OpenMAMA with the **Solace middleware bridge**. This tutorial's samples are similar to the OpenMAMA's [mamapublisherc.c](https://github.com/OpenMAMA/OpenMAMA/blob/master/mama/c_cpp/src/examples/c/mamapublisherc.c) and [mamasubscriberc.c](https://github.com/OpenMAMA/OpenMAMA/blob/master/mama/c_cpp/src/examples/c/mamasubscriberc.c), but with a distinct focus on configuring OpenMAMA with **Solace message routers**. See the [Resources](#resources) section below for some further links to other OpenMAMA tutorials and examples.

This tutorial will show you how to publish a message with one string field to a specific topic on a Solace message router using OpenMAMA C API.

This tutorial will show you how to use OpenMAMA C API:
* to build and send a message on a topic
* to subscribe to a topic and receive a message

## Installation

Installation instructions for OpenMAMA can be found on [OpenMAMA Wiki](http://www.openmama.org/content/quick-start-guide#main).

Simplified installation instructions for OpenMAMA with Solace middleware bridge [are available](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/install.md).

For building OpenMAMA from source see [OpenMAMA Wiki](https://github.com/OpenMAMA/OpenMAMA/wiki/Build-Instructions).

---

## Getting Access to Solace Message Router

There are two ways you can get hold of the **Solace message router**:
- If your company has Solace message routers deployed, contact your middleware team to obtain the host name or IP address of a Solace message router to test against, a username and password to access it, and a VPN in which you can produce and consume messages.
- If you do not have access to a Solace message router, you will need to use the [Solace Virtual Router](http://www.solacesystems.com/products/solace-virtual-message-router). Go through the “[Set up a VMR](http://dev.solacesystems.com/get-started/vmr-setup-tutorials/setting-up-solace-vmr/)” tutorial to download and install it. By default the Solace VMR will run with the `“default”` message VPN configured and ready for messaging.

Going forward, this tutorial assumes that you are using the Solace VMR. If you are using a different Solace message router configuration, adapt the instructions to match your configuration.

---

## Solace Message Router Properties

In order to send or receive messages to a Solace message router, you need to know a few details of how to connect to the Solace message router. Specifically you need to know the following:

Resource | Value | Description
------------ | ------------- | -------------
Host | String of the form `DNS name` or `IP:Port` | This is the address clients use when connecting to the Solace message router to send and receive messages. For a Solace VMR this there is only a single interface so the IP is the same as the management IP address. For Solace message router appliances this is the host address of the message-backbone.
Message VPN | String | The Solace message router Message VPN that this client should connect to. The simplest option is to use the `default` message-vpn which is present on all Solace message routers and fully enabled for message traffic on Solace VMRs.
Client Username | String | The client username. For the Solace VMR default message VPN, authentication is disabled by default, so this can be any value.
Client Password | String | The optional client password. For the Solace VMR default message VPN, authentication is disabled by default, so this can be any value or omitted.

Example of specifying these properties [see here](https://github.com/dfedorov-solace/solace-openmama-publish-subscribe/blob/master/mama.properties) and detailed explanation of them is in the [Solace OpenMAMA "Hello World" tutorial](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md#properties-file).

---

## Trying It Yourself

This tutorial is available in [GitHub](https://github.com/dfedorov-solace/solace-openmama-publish-subscribe).

Clone the GitHub repository containing the tutorial.

```
$ git clone git://github.com/dfedorov-solace/solace-openmama-publish-subscribe.git
$ cd solace-openmama-publish-subscribe
```

To build on **Linux** (with `GCC`), assuming OpenMAMA installed into `/opt/openmama`:
```
$ gcc -o topicSubscriber topicSubscriber.c -I/opt/openmama/include -L/opt/openmama/lib -lmama
$ gcc -o topicPublisher topicPublisher.c -I/opt/openmama/include -L/opt/openmama/lib -lmama
```

To build on **Windows** (with `cl`), assuming OpenMAMA is at `<openmama>` directory:
```
$ cl topicSubscriber.c /I<openmama>\mama\c_cpp\src\c /I<openmama>\common\c_cpp\src\c\windows -I<openmama>\common\c_cpp\src\c <openmama>\Debug\libmamacmdd.lib
$ cl topicPublisher.c /I<openmama>\mama\c_cpp\src\c /I<openmama>\common\c_cpp\src\c\windows -I<openmama>\common\c_cpp\src\c <openmama>\Debug\libmamacmdd.lib
```

To run the application on **Linux**:

```
$ ./topicSubscriber
$ ./topicPublisher
```

To run the application on **Windows**:

```
$ topicSubscriber.exe
$ topicPublisher.exe
```

---

##Receiving Message

Before everything else, as you already know from the [Solace OpenMAMA "Hello World" tutorial](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md), we need to [initialize](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md#initialize) the **Solace middleware bridge** and [create a transport](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md#create-transport).

To learn more about how to initialize and configure **Solace middleware bridge as an OpenMAMA transport** see [Solace OpenMAMA User Guide](https://sftp.solacesystems.com/Portal_Docs/index.html#page/OpenMAMA_User_Guide/Configuring_Solace_OpenMAMA_Bridges.html)

Now we can implement receiving a message.

First, let’s express interest in messages by subscribing to a topic. Then you can look at publishing a matching message and see it received.

In OpenMAMA subscribing to a topic means *creating a subscription*. When creating a subscription, we need to refer to a *transport*, a *receiver* and to an *event queue*.

You already know what the **transport** is.

For details of the **event queue** see [OpenMAMA Developer's Guide for C](http://www.openmama.org/sites/default/files/OpenMAMA%20Developer%27s%20Guide%20C.pdf)

When multi-threading is not required it is recommended to use the *default internal event queue* as an *event queue*:

```c
mamaQueue defaultQueue;
mama_getDefaultEventQueue(global.bridge, &defaultQueue))
```

The **receiver** is a data structure with function pointers that are invoked by OpenMAMA when certain events happen.

It has a type of `mamaMsgCallbacks` and it is expected to have, as a minimum, the following function pointers:

* `onCreate` that is invoked when a subscription is created
* `onError` that is invoked when an error occurs
* `onMsg` that is invoked when a message arrives on the subscribed topic

This is how the routine for creating a subscription is implemented:

```c
void subscribeToTopic(const char * topicName)
{
    global.subscription = NULL;
    mama_status status;
    if ((status = mamaSubscription_allocate(&global.subscription)) == MAMA_STATUS_OK)
    {
        // initialize functions called by MAMA on different subscription events
        memset(&global.receiver, 0, sizeof(global.receiver));
        global.receiver.onCreate = onCreate;
        global.receiver.onError = onError;
        global.receiver.onMsg = onMessage;

        mamaQueue defaultQueue;
        if (((status = mama_getDefaultEventQueue(global.bridge, &defaultQueue)) == MAMA_STATUS_OK) &&
            ((status = mamaSubscription_createBasic(global.subscription, global.transport,
                                                    defaultQueue,
                                                    &global.receiver,
                                                    topicName,
                                                    NULL)) == MAMA_STATUS_OK))
        {
            // normal exit
            return;
        }
    }
    // error exit
    printf("Error subscribing to topic %s, error code: %s\n", topicName,
            mamaStatus_stringForStatus(status));
    mama_close();
    exit(status);
}
```

Notice that `global.receiver` is assigned with pointers to functions we want to be invoked by OpenMAMA when corresponding events happen.

When a subscription is created, we want to see its topic name on the console:

```c
void onCreate(mamaSubscription subscription, void * closure)
{
    const char * topicName = NULL;
    if (mamaSubscription_getSymbol(subscription, &topicName) == MAMA_STATUS_OK)
    {
        printf("Created subscription to topic \"%s\"\n", topicName);
    }
}
```

When an error occurs, we want to see on the console the error code:

```c
void onError(mamaSubscription subscription, mama_status status,
        void * platformError, const char * subject, void * closure)
{
    const char * topicName = NULL;
    if (mamaSubscription_getSymbol(subscription, &topicName) == MAMA_STATUS_OK)
    printf("Error occured with subscription to topic \"%s\", error code: %s\n",
            topicName, mamaStatus_stringForStatus(status));
}
```

When a message arrives, we extract from the message the topic name and a custom string field we know was added to that message:

```c
void onMessage(mamaSubscription subscription, mamaMsg message, void * closure, void * itemClosure)
{
    const char * topicName = NULL;
    if (mamaSubscription_getSymbol(subscription, &topicName) == MAMA_STATUS_OK)
    {
        printf("Message of type %s received on topic \"%s\"\n", mamaMsgType_stringForMsg(message), topicName);
    }
    // extract from the message our own custom field
    const char * const MY_FIELD_NAME = "MdMyTimestamp";
    const int MY_FIELD_ID = 99, BUFFER_SIZE = 32;
    char buffer[BUFFER_SIZE];
    if (mamaMsg_getFieldAsString(message, MY_FIELD_NAME, MY_FIELD_ID, buffer, BUFFER_SIZE)
            == MAMA_STATUS_OK)
    {
        printf("This message has a field \"%s\" with value: %s", MY_FIELD_NAME, buffer);
    }
}
```

Now we can start receiving messages:

```c
mama_start(global.bridge);
```

This `mama_start` call blocks execution of the current thread until `mama_stop` is called, that is why we need to implement a handler for stopping our program by pressing `Ctrl-C` from console.

```c
void stopHandler(int value)
{
    signal(value, SIG_IGN);
    printf(" Do you want to stop the program? [y/n]: ");
    char answer = getchar();
    if (answer == 'y')
        stopAll();
    else
        signal(SIGINT, stopHandler);
    getchar();
}
```

The `stopAll` routine has `destroy` calls for the created transport and subscription.

Order of calls in that routine is very important and the very first one must be `mama_stop`:

```c
void stopAll()
{
    mama_stop(global.bridge);
    if (global.subscription)
    {
        mamaSubscription_destroy(global.subscription);
        global.subscription = NULL;
    }
    if (global.transport)
    {
        mamaTransport_destroy(global.transport);
        global.transport = NULL;
    }
}
```

---

##Sending Message

As usual, before everything else, we need to [initialize](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md#initialize) the **Solace middleware bridge** and [create a transport](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md#create-transport).

Now we can implement sending a message, and as you already know from the [Solace OpenMAMA "Hello World" tutorial](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md#create-publisher), to publish a message we need to create a *publisher*.

A *publisher* is created for a specific topic and refers to already created *transport*:

```c
global.publisher = NULL;
mamaPublisher_create(&global.publisher, global.transport, publishTopicName, NULL, NULL)
```

We want our program to publish messages periodically, until we stop it (the program by pressing `Ctrl-C` from console).

OpenMAMA provides a very convenient mechanism for a periodic event in a forms of *mamaTimer*. Such timers created with a reference to an *event queue* and we can use the *default internal event queue* for it:

```c
global.publishTimer = NULL;
mamaTimer_create(&global.publishTimer, defaultQueue, timerCallback, intervalSeconds, NULL);
```

The `timerCallback` parameter is a pointer to a function that will be invoked by OpenMAMA when the timer expires.

We're going to implement this function to send one message with three different fields, one of them is a custom field with the message timestamp as a string:

```c
void timerCallback(mamaTimer timer, void* closure)
{
    // generate a timestamp as one of the message fields
    char buffer[32];
    time_t currTime = time(NULL);
    sprintf(buffer, "%s", asctime(localtime(&currTime)));
    // create message and add three fields to it
    mamaMsg message = NULL;
    mama_status status;
    if (((status = mamaMsg_create(&message)) == MAMA_STATUS_OK) &&
        ((status = mamaMsg_addI32(message, MamaFieldMsgType.mName, MamaFieldMsgType.mFid,
                                  MAMA_MSG_TYPE_INITIAL)) == MAMA_STATUS_OK) &&
        ((status = mamaMsg_addI32(message, MamaFieldMsgStatus.mName, MamaFieldMsgStatus.mFid,
                                  MAMA_MSG_STATUS_OK)) == MAMA_STATUS_OK) &&
        ((status = mamaMsg_addString(message, "MdMyTimestamp", 99,
                                     buffer)) == MAMA_STATUS_OK))
    {
        if ((status = mamaPublisher_send(global.publisher, message)) == MAMA_STATUS_OK)
        {
            printf("Message published: %s", buffer);
            mamaMsg_destroy(message);
            // normal exit
            return;
        }
    }
    // error exit
    printf("Error publishing message: %s\n", mamaStatus_stringForStatus(status));
}
```

Now we can start sending messages:

```c
mama_start(global.bridge);
```

Because `mama_start` call blocks execution of the current thread until `mama_stop` is called, we're going to use the same, handler, mechanism for stopping our program by pressing `Ctrl-C` from console.

That handler calls `stopAll`. As you already know, order of calls in `stopAll` routine is very important and the very first one must be `mama_stop`:

```c
void stopAll()
{
    mama_stop(global.bridge);
    // order is important
    if (global.publishTimer)
    {
        mamaTimer_destroy(global.publishTimer);
        global.publishTimer = NULL;
    }
    if (global.publisher)
    {
        mamaPublisher_destroy(global.publisher);
        global.publisher = NULL;
    }
    if (global.transport)
    {
        mamaTransport_destroy(global.transport);
        global.transport = NULL;
    }
}
```

---

##Summarizing

Our application consists of two executables: *topicSubscriber* and *topicPublisher*.

If you combine the example source code shown above and split them into the two mentioned executables, it results in the source that is available for download:

* [topicSubscriber](https://github.com/dfedorov-solace/solace-openmama-publish-subscribe/blob/master/topicSubscriber.c)
* [topicPublisher](https://github.com/dfedorov-solace/solace-openmama-publish-subscribe/blob/master/topicPublisher.c)

###Getting the Source

Clone the GitHub repository containing the source.

```
git clone git://github.com/dfedorov-solace/solace-openmama-publish-subscribe
cd solace-openmama-publish-subscribe
```

###Building

To build on **Linux**, assuming OpenMAMA installed into `/opt/openmama`:
```
$ gcc -o topicSubscriber topicSubscriber.c -I/opt/openmama/include -L/opt/openmama/lib -lmama
$ gcc -o topicPublisher topicPublisher.c -I/opt/openmama/include -L/opt/openmama/lib -lmama
```

###Running the Application

First, start the *topicSubscriber*:

```
$ ./topicSubscriber 
Solace OpenMAMA tutorial.
Receiving messages with OpenMAMA.
2016-07-14 14:22:38: 
********************************************************************************
Note: This build of the MAMA API is not enforcing entitlement checks.
Please see the Licensing file for details
**********************************************************************************
2016-07-14 14:22:38: mamaTransport_create(): No entitlement bridge specified for transport vmr. Defaulting to noop.
Created subscription to topic "tutorial.topic"
```

Now in a separate console start the *topicPublisher* that begins publishing messages periodically:

```
$ ./topicPublisher 
Solace OpenMAMA tutorial.
Publishing messages with OpenMAMA.
2016-07-14 14:22:48: 
********************************************************************************
Note: This build of the MAMA API is not enforcing entitlement checks.
Please see the Licensing file for details
**********************************************************************************
2016-07-14 14:22:48: mamaTransport_create(): No entitlement bridge specified for transport vmr. Defaulting to noop.
Message published: Thu Jul 14 14:22:51 2016
Message published: Thu Jul 14 14:22:54 2016
Message published: Thu Jul 14 14:22:57 2016
Message published: Thu Jul 14 14:23:00 2016
```

You will see the *topicSubscriber* receiving messages and logging them to the connsole:

```
$ ./topicSubscriber 
Solace OpenMAMA tutorial.
Receiving messages with OpenMAMA.
2016-07-14 14:22:38: 
********************************************************************************
Note: This build of the MAMA API is not enforcing entitlement checks.
Please see the Licensing file for details
**********************************************************************************
2016-07-14 14:22:38: mamaTransport_create(): No entitlement bridge specified for transport vmr. Defaulting to noop.
Created subscription to topic "tutorial.topic"
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:22:51 2016
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:22:54 2016
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:22:57 2016
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:23:00 2016
```

To stop the application press `Ctrl-C` and then `y` on each console. First, stop the publisher:

```
$ ./topicPublisher 
Solace OpenMAMA tutorial.
Publishing messages with OpenMAMA.
2016-07-14 14:22:48: Failed to open properties file.

2016-07-14 14:22:48: 
********************************************************************************
Note: This build of the MAMA API is not enforcing entitlement checks.
Please see the Licensing file for details
**********************************************************************************
2016-07-14 14:22:48: mamaTransport_create(): No entitlement bridge specified for transport vmr. Defaulting to noop.
Message published: Thu Jul 14 14:22:51 2016
Message published: Thu Jul 14 14:22:54 2016
Message published: Thu Jul 14 14:22:57 2016
Message published: Thu Jul 14 14:23:00 2016
^C Do you want to stop the program? [y/n]: y
Closing Solace middleware bridge.
```

Now we can stop the subscriber:

```
$ ./topicSubscriber 
Solace OpenMAMA tutorial.
Receiving messages with OpenMAMA.
2016-07-14 14:22:38: 
********************************************************************************
Note: This build of the MAMA API is not enforcing entitlement checks.
Please see the Licensing file for details
**********************************************************************************
2016-07-14 14:22:38: mamaTransport_create(): No entitlement bridge specified for transport vmr. Defaulting to noop.
Created subscription to topic "tutorial.topic"
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:22:51 2016
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:22:54 2016
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:22:57 2016
Message of type INITIAL received on topic "tutorial.topic"
This message has a field "MdMyTimestamp" with value: Thu Jul 14 14:23:00 2016
^C Do you want to stop the program? [y/n]: y
Closing Solace middleware bridge.
```

Congratulations! You have now successfully subscribed to a topic and exchanged messages using this topic on using OpenMAMA with the Solace middleware bridge.

If you have any issues with this program, check the [Solace community](http://dev.solacesystems.com/community/) for answers to common issues.

---

## Resources

For more information about OpenMAMA:

- The OpenMAMA website at: [http://www.openmama.org/](http://www.openmama.org/).
- The OpenMAMA code repository on GitHub [https://github.com/OpenMAMA/OpenMAMA](https://github.com/OpenMAMA/OpenMAMA).
- Chat with OpenMAMA developers and users at [Gitter OpenMAMA room](https://gitter.im/OpenMAMA/OpenMAMA).

For more information about Solace technology:

- The Solace Developer Portal website at: [http://dev.solacesystems.com](http://dev.solacesystems.com/)
- Get a better understanding of [Solace technology](http://dev.solacesystems.com/tech/).
- Check out the [Solace blog](http://dev.solacesystems.com/blog/) for other interesting discussions around Solace technology.
- Ask the [Solace community](http://dev.solacesystems.com/community/).

Other tutorials and samples:

*   [OpenMAMA Quick Start Guide](http://www.openmama.org/content/quick-start-guide) 
*   [OpenMAMA Wiki Quick Start Guide](https://github.com/OpenMAMA/OpenMAMA/wiki/Quick-Start-Guide)
*   [OpenMAMA Example Walk Through](http://www.openmama.org/example-walk-through).
*   [OpenMAMA Code Examples](https://github.com/OpenMAMA/OpenMAMA/tree/master/mama/c_cpp/src/examples) 
*   [OpenMAMA Wiki](https://github.com/OpenMAMA/OpenMAMA/wiki)
*   [OpenMAMA Documentation and Developers Guides](http://www.openmama.org/documentation)
*   [Solace OpenMAMA Hello World](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/hello-world.md)
*   [Solace's Solution for OpenMAMA](http://dev.solacesystems.com/tech/openmama/)
