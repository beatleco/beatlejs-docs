# Container Methods

The container provides categorized methods for efficient service and plugin management. Below we detail each functional group with usage examples

### Service Retrieval

#### `getByClass(serviceDefinition)`

Retrieves a **service instance** by passing its **definition class** and returns an **instance of the service**.

#### `getByName(serviceIdentifier)`

Retrieves a **service instance** by using its **identifier** and returns an **instance of the service**.

{% hint style="warning" %}
Both methods return the **default instance** of the service by default. However, if a **scope** argument is provided, they return a scoped instance.
{% endhint %}

#### `resolveByClass(serviceDefinition)`

Similar to `getByClass()`, but operates asynchronously. This method **waits for all plugins** in the service to initialize before returning an **instance of the service**.

```javascript
const GreetingService = Service({
    identifier: 'Greeting',
}, {
    sayHello: func(function sayHello() {
        console.log(`Hello from Beatles!`);
    }),
});

const container = Container();

expect.toEqual(
    container.getByClass(GreetingService),
    container.getByName('Greeting'),
)

const instance = await container.resolveByClass(GreetingService)

instance.sayHello()
// Hello from Beatles!
```

***

### Plugin Management

#### `getPluginByClass(pluginDefinition)`

Retrieves a **plugin instance** by passing its **definition class** and returns an **instance of the plugin**.

#### `registerPlugin(pluginDefinition)`

Registers a **dynamic** plugin that is not part of the global static registry.

{% hint style="danger" %}
A plugin might be imported but **not initialized** yet. Calling `registerPlugin()` ensures it is initialized properly.
{% endhint %}

```javascript

const container = Container();


function RetryPlugin() {
  return {
    greetings: 'Hello from retry plugin!',
    async onCreate(target: BServiceClass, instance: BServiceInstance<never>) { },
    async onDestroy(target: BServiceClass, instance: BServiceInstance<never>) { },
  };
}
// register the plugin into the registries
extendPlugins(RetryPlugin);
container.registerPlugin(RetryPlugin);

const plugin = container.getPluginByClass(RetryPlugin);
console.log(plugin.greetings)

// Hello from retry plugin!
```

***

### Service Execution

#### `invokeParallel(methodName)`

Calls a method (given by `methodName`) across **all registered services** within the container **simultaneously**.

#### `invokeLinear(methodName)`

Calls a method (given by `methodName`) across **all registered services** within the container one after another with respect to each service order.

```javascript
Service({ 
    identifier: 'ServiceA',
    order: 20,
}, {
    sayHello: func(async function sayHello() {
        await delay(10_000);
        console.log(`Hello from ServiceA!`);
    }),
});

Service({
    identifier: 'ServiceB',
    order: 10,
}, {
    sayHello: func(async function sayHello() {
        await delay(10_000);
        console.log(`Hello from ServiceB!`);
    }),
});

const container = Container();


await container.invokeLinear('sayHello');
// (waits 10 seconds)
// Hello from ServiceB!
// (waits another 10 seconds)
// Hello from ServiceA!


await container.invokeParallel('sayHello');
// (waits total 10 seconds)
// Hello from ServiceB!
// Hello from ServiceA!
```

***

### Container Lifecycle Management

`reset(filters?)`

Resets all service instances in the container, restoring them to their initial state.

#### destroy(`filters?`)

Completely removes all **service instances**, **emptying the container**.

{% hint style="warning" %}
If filters are provided, only the **specified services** are going to reset or destroy. Otherwise, the entire container is cleared.
{% endhint %}

```javascript
const ServiceA = Service({ 
    identifier: 'ServiceA',
}, {
    data: val('Hello World!'),
});

const container = Container();


var svcA = container.getByName('ServiceA');
console.log(svcA.data); // Hello World!

svcA.data = 'temp!';
console.log(svcA.data); // Temp!!

// resets all services to their initial state
container.reset();
console.log(svcA.data); // Hello World!

container.destroy();

console.log(container.getByName('ServiceA').data); 
// ServiceA: Service is not registered within the global scope
```

{% hint style="info" %}
Difference between `reset()` and `destroy()`

* `reset()`: Resets **service instances** but keeps them in the container.
* `destroy()`: Completely removes **service instances** from the container.
{% endhint %}

***

### Container Storage

#### setProperty`(key, value)`

Stores a variable inside the container (e.g., environment variables).

#### get`Property(key)`

Retrieves a stored variable from the container.

```javascript

const container = Container();
container.setProperty('test', 123);

console.log(container.getProperty('test')) // 123
```

