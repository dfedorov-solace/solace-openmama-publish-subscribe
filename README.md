# Solace OpenMAMA Publish Subscribe

---

## Publish/subscribe messaging application using OpenMAMA with Solace middleware bridge

This application will show you how to use **OpenMAMA** to add a topic subscription on a **Solace message router** and publish a message matching this topic subscription.

To download and install **OpenMAMA** see its [Quick Start Guide](http://www.openmama.org/content/quick-start-guide) and [these instructions](https://github.com/dfedorov-solace/solace-openmama-hello-world/blob/master/_docs/install.md).

There are two ways you can get hold of the **Solace message router**:
- If your company has Solace message routers deployed, contact your middleware team to obtain the host name or IP address of a Solace message router to test against, a username and password to access it, and a VPN in which you can produce and consume messages.
- If you do not have access to a Solace message router, you will need to use the [Solace Virtual Router](http://www.solacesystems.com/products/solace-virtual-message-router). Go through the “[Set up a VMR](http://dev.solacesystems.com/get-started/vmr-setup-tutorials/setting-up-solace-vmr/)” tutorial to download and install it.

## Content

This repository contains [subscriber](topicSubscriber.c) and [publisher](topicPublisher.c) code, [sample configuration](mama.properties) and [matching tutorial](_docs/publish-subscribe.md).

## Checking out and Building

To check out the project and build it, do the following:

```
git clone git://github.com/dfedorov-solace/solace-openmama-publish-subscribe
cd solace-openmama-publish-subscribe
```

To build on **Linux**, assuming OpenMAMA installed into `/opt/openmama`:
```
$ gcc -o topicSubscriber topicSubscriber.c -I/opt/openmama/include -L/opt/openmama/lib -lmama
$ gcc -o topicPublisher topicPublisher.c -I/opt/openmama/include -L/opt/openmama/lib -lmama
```

To build on **Windows**, assuming OpenMAMA is at `<openmama>` directory:
```
$ cl topicSubscriber.c /I<openmama>\mama\c_cpp\src\c /I<openmama>\common\c_cpp\src\c\windows -I<openmama>\common\c_cpp\src\c <openmama>\Debug\libmamacmdd.lib
$ cl topicPublisher.c /I<openmama>\mama\c_cpp\src\c /I<openmama>\common\c_cpp\src\c\windows -I<openmama>\common\c_cpp\src\c <openmama>\Debug\libmamacmdd.lib
```

## Running the application

On **Linux**:

```
$ ./topicSubscriber
$ ./topicPublisher
```

On **Windows**:

```
$ topicSubscriber.exe
$ topicPublisher.exe
```

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
