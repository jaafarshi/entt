# Crash Course: service locator

<!--
@cond TURN_OFF_DOXYGEN
-->
# Table of Contents

* [Introduction](#introduction)
* [Service locator](#service-locator)
<!--
@endcond TURN_OFF_DOXYGEN
-->

# Introduction

Usually service locators are tightly bound to the services they expose and it's
hard to define a general purpose solution. This template based implementation
tries to fill the gap and to get rid of the burden of defining a different
specific locator for each application.<br/>
This class is tiny, partially unsafe and thus risky to use. Moreover it doesn't
fit probably most of the scenarios in which a service locator is required. Look
at it as a small tool that can sometimes be useful if users know how to handle
it.

# Service locator

The API is straightforward. The basic idea is that services are implemented by
means of interfaces and rely on polymorphism.<br/>
The locator is instantiated with the base type of the service if any and a
concrete implementation is provided along with all the parameters required to
initialize it. As an example:

```cpp
// the service has no base type, a locator is used to treat it as a kind of singleton
entt::ServiceLocator<MyService>::set(params...);

// sets up an opaque service
entt::ServiceLocator<AudioInterface>::set<AudioImplementation>(params...);

// resets (destroys) the service
entt::ServiceLocator<AudioInterface>::reset();
```

The locator can also be queried to know if an active service is currently set
and to retrieve it if necessary (either as a pointer or as a reference):

```cpp
// no service currently set
auto empty = entt::ServiceLocator<AudioInterface>::empty();

// gets a (possibly empty) shared pointer to the service ...
std::shared_ptr<AudioInterface> ptr = entt::ServiceLocator<AudioInterface>::get();

// ... or a reference, but it's undefined behaviour if the service isn't set yet
AudioInterface &ref = entt::ServiceLocator<AudioInterface>::ref();
```

A common use is to wrap the different locators in a container class, creating
aliases for the various services:

```cpp
struct Locator {
    using Camera = entt::ServiceLocator<CameraInterface>;
    using Audio = entt::ServiceLocator<AudioInterface>;
    // ...
};

// ...

void init() {
    Locator::Camera::set<CameraNull>();
    Locator::Audio::set<AudioImplementation>(params...);
    // ...
}
```
