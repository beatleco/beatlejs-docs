# Overview

Modern frontend applications often struggle with complexity as they scale. Multiple stores, nested selectors, and endless event handling patterns can make large codebases hard to maintain, debug, and extend. BeatleJS approaches this problem differently by introducing a **service-oriented architecture (SOA)** to the frontend—where application logic is delegated to modular, loosely coupled services instead of sprawling state containers and tangled event systems.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption><p>Comparing different approaches to state handling</p></figcaption></figure>

At the heart of BeatleJS is the concept of a **service**—a structured, self-contained unit that holds both **state** and **behavior**. Services are designed to be **reactive, type-safe, and extensible**. Each service can define its properties and methods with decorators like `val` and `func`, and extend its functionality through plugins without altering its core structure. This makes services **highly composable** and easy to reason about, even in large-scale applications.

<pre class="language-javascript"><code class="lang-javascript">// service example pseudo code

<strong>const PaymentService = Service(
</strong>    /* implementation redacted */
);

const OrderService = Service({
    identifier: "Order"
}, {
    cart: val([]),
    addToCart: func(addToCart),
    checkout: func(checkout),
})

async function addToCart(productId) {
    const product = await fetch(`/products/${productId}`)
    this.cart.push(product)
}

async function checkout() {
    const info = await fetch(`/order/checkout`, { cart: this.cart })
    const paymentService = this.container.getByClass(PaymentService)
    await paymentService.start(info)
}
</code></pre>

To manage these services, BeatleJS uses a **lightweight container**. This container automatically handles **service registration, scoping, and lifecycle management**, allowing you to create isolated service instances for different contexts—or share a single instance across the entire application when needed. This flexibility is key for building scalable architectures, where changes to one part of the system don’t cascade into unintended side effects elsewhere.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>Each service is accessible anywhere that there is an access to the container</p></figcaption></figure>

Plugins play a central role in BeatleJS’s **extensible architecture**. Every additional capability—whether it’s persistence, reactive signals, debouncing, or retry logic—comes from plugins. Developers can use built-in plugins or create custom ones, ensuring that the framework adapts to the project’s needs without introducing unnecessary bloat. Combined with **native lazy loading and code splitting**, this plugin-driven design keeps your application fast and your bundle size minimal.

```javascript
const OrderService = Service({
    identifier: "Order"
}, {
    cart: persist(signal(val([]))),
    addToCart: debounce(func(addToCart), 250),
    checkout: retry(func(checkout)),
})
```

Finally, BeatleJS is built with **TypeStrong principles** at its core. From services to decorators and lifecycle hooks, every piece is fully typed, making it easier to debug, test, and refactor your application confidently. Combined with its reactive, modular design and framework-agnostic nature, BeatleJS provides a foundation for building **clean, scalable, and maintainable applications** that grow with your needs.

<figure><img src="../.gitbook/assets/Frame 2.png" alt=""><figcaption><p>Service properties and functions are type-inferred and can be traced using modern IDEs</p></figcaption></figure>

