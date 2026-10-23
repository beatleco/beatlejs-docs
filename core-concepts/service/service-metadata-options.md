# Service Metadata (Options)

BeatleJS allows you to **attach metadata** to different parts of a service. This metadata is passed through the **options** argument when registering the service, and controls how that service behaves inside the app.

Let’s walk through the key metadata fields.

***

### Identifier

Every service in BeatleJS **must** **declare** a unique identifier. This ensures that the service can always be found and referenced reliably, even in complex environments where class names or variables might not be consistent.

JavaScript variable names aren't dependable for identification in the long run. That's why BeatleJS uses a stable, developer-defined string identifier instead.

Using identifiers is especially important when:

* **Third-party integration :** External tools or bundlers may rename or obscure service names during the build process, making variable names unreliable.
* **Serialization and deserialization :** When persisting or restoring service state, the system needs a fixed identifier to know which service the data belongs to.
* **Decoupled access :** Services can be invoked or resolved without holding a direct reference to their original class or variable — identifiers make this possible.

```javascript
const ServiceA = Service({
    identifier: 'ServiceCalledA'
}, {})

```

{% hint style="warning" %}
Lazy-loaded services require special handling. Until a service is accessed directly at least once, its identifier won’t be registered. That means identifier-based access won’t work until the service has been initialized.
{% endhint %}

***

### Version

Services in BeatleJS can optionally declare a version in their metadata. This isn't required for most use cases, but it becomes useful when working with evolving service logic, plugin systems, or debugging tools that need to distinguish between multiple versions of the same service.

Declaring a version allows:

* **Version awareness :** Track which version of a service is currently active within the system.
* **Version-specific logic :** Apply conditional behavior based on the version, either internally or through plugins.

By default, if no version is provided, the framework still handles the service normally — but adding a version gives you more control and opens the door to version-sensitive features.

```javascript
const ServiceA = Service({
    identifier: 'ServiceCalledA',
    version: 2,
}, {})
```

{% hint style="success" %}
* Some plugins may rely on the service version to determine compatibility or behavior.
* If no version is defined, BeatleJS assigns and manages it internally.
* Versioning is fully manual — it's up to the developer to assign and update versions when needed.
{% endhint %}

***

### Order

The **order** metadata controls the **execution priority of methods** when calling `invokeLinear()` inside a service. This lets you define exactly which method should run first, second, and so on.

By default, if no **order** is defined for the methods, their execution order is determined by the internal registration order — which may not be predictable or consistent across environments.

This matters most when certain methods depend on the results of others. Using **order** ensures those relationships are respected.

Here’s how it works:

* **Linear invocation (`invokeLinear`)**\
  Methods run one at a time in ascending **order** value. Each method must fully complete before the next one starts.
* **Parallel invocation (`invokeParallel`)**\
  All methods run simultaneously. The **order** metadata is ignored.

While linear execution ensures controlled sequencing, it comes with a performance cost — each method blocks the next. Parallel execution is generally faster but doesn’t guarantee any order at all.

```javascript
const Net = Service({
    identifier: 'Network',
    order: -10002,
}, {})

const Auth = Service({
    identifier: 'Authentication',
    version: 2,
    order: -10000,
}, {})
```

{% hint style="danger" %}
* if no **order** is set, methods run based on their registration priority — which can appear random.
* `invokeParallel()` always ignores **order**, making it ideal for independent, non-blocking methods.
* Use **order** only when method dependencies require strict sequencing.
{% endhint %}
