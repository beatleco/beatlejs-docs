---
description: core concepts of the BeatleJs
---

# Core Concepts

### How Does BeatleJS Work?

BeatleJS provides **automatic module registration**, unlike frameworks like NestJS and other dependency injection (DI) frameworks that enforce strict service registration within defined modules. BeatleJS simplifies this process by managing registrations within the **“module scope.”** This means service definitions are shared across all imports withing the same runtime environment&#x20;

***

### Service Registration Lifecycle

Defining a service in BeatleJS involves three key stages:

1. **Service Definition**

This is done by calling the `Service()` function to create a service definition.

```javascript
const OrderService = Service({
    identifier: "Order"
}, {
    cart: val([]),
    addToCart: func(addToCart),
    checkout: func(checkout),
})
```

2. **Service Registration**

When the `Service()` is called, the definition is registered into the **global registries** (data structures that store registration details within the runtime environment).

```javascript
const ServiceA = Service(/* ... */);
const ServiceB = Service(/* ... */);

// global registries are now contain a reference to these service definitions
```

{% hint style="danger" %}
Service definitions and service instances are two distinct entities. Definitions describe the service’s structure, while instances are live implementations.
{% endhint %}

3. **Service Instantiation**

This occurs when the `Container` constructor is called, initializing the registries that contain one or more service definitions.

```javascript
// service definition are registered into
// the global beatle registries
const ServiceA = Service(/* ... */);
const ServiceB = Service(/* ... */);

// container initializes the existing services
// (in this case: ServiceA and ServiceB)
// and store an instance of those services within the container
const container = Container();
```

{% hint style="info" %}
The point is, to have access to all existing services after calling the container, for example, consider a case you want to call a function on all services, this requires services to be registered before the container initialization.

```javascript
container.invokeLinear('hello');
```
{% endhint %}

{% hint style="info" %}
The `Container` constructor is best to be called after importing BeatleJS and other services.
{% endhint %}

***

### Types of Services

BeatleJS supports two types of services, determined by when they are registered:

* **Static Services**

Registered **before** the container is initialized and immediately initialized when the container is ready.

* **Runtime Services**

Registered **after** the container is initialized. These are initialized only when explicitly acquired using container methods.

```javascript
// Statically registered services
const ServiceA = Service(/* ... */);
const ServiceB = Service(/* ... */);

const container = Container();

// Dynamically registered services (not initialized until explicit init call)
const ServiceC = Service(/* ... */);
const ServiceD = Service(/* ... */);

// it is required to explicitly acquire the services
// so they could become initialized
const svcC = container.getByClass(ServiceC);
```

{% hint style="info" %}
The acquired function always returns an instance of the desired service, regardless of its type.
{% endhint %}

***

### Why Use Containers for Service Instances?

Service instances are initialized within containers for the following reasons:

* To prevent memory leaks.
* To enable the creation of multiple containers in the runtime environment, if needed.

While service definitions are registered globally, their **instances reside in individual containers**, each maintaining its own state and variables.

```javascript
const ServiceA = Service(/* ... */);
const ServiceB = Service(/* ... */);

const container1 = Container();
const container2 = Container();

expect.not.toEqual(
    container1.getByClass(ServiceA),
    container2.getByClass(ServiceA)
)

```

{% hint style="success" %}
It is recommended to use a single container in an application. However, you can explore other configurations depending on your application’s needs.
{% endhint %}

***

### Sharing Services Across Projects

Services in BeatleJS are designed to be independent, making them reusable across multiple projects. This enhances flexibility and reduces redundancy in implementation.
