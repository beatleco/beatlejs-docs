# Service Prototype

The service prototype defines the structure and behavior of a service — much like a constructor does in a class. It’s where you describe the service’s internal state, its available methods, and how those pieces interact.

BeatleJS uses a simple and expressive syntax based on decorators. There are two primitive decorators that form the foundation of every service:

* **`val()`** is used to declare reactive, stateful properties. It’s type smart — meaning it can infer and preserve types automatically, making your service logic more robust without extra boilerplate.
* **`func()`** is used to define executable methods — anything that performs an action, reacts to a signal, or runs logic.

Together, these two decorators give you everything you need to model a service's logic in a clean, declarative way.

```javascript
const OrderService = Service({
    identifier: "Order"
}, {
    cart: val([]),
    addToCart: func(addToCart),
    checkout: func(checkout),
})
```

{% hint style="success" %}
Both `val` and `func` are extensible. Plugins can enhance them by layering in additional behavior — like persistence, reactivity, scheduling, and more. This extension system lets you build on top of the core decorators to match the needs of your app.
{% endhint %}
