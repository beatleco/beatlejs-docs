# Service Scopes

By default, services in BeatleJS are singletons. This means that when you access a service anywhere in your application, you're always working with the same shared instance — one copy of its state, shared across the app.

That works well in many cases. But sometimes, you need multiple versions of the same service, each with its own completely isolated state. BeatleJS makes this possible through **scoping**.

Let’s say you're building a chat system. You define a single chat service that tracks:

* Active conversations
* Online status
* Timestamps for the last received message

That works fine — until a user opens two chat windows at the same time. Suddenly, you run into strange issues:

* One user's conversation list appears in another's window
* Online statuses get mixed
* The most recent message overwrites everything, regardless of which chat it belongs to

The problem? All those chat windows are sharing a single service instance.

The solution is to create **scoped service instances** — separate copies of the same service, each tied to a different context. With BeatleJS, you can pass a unique scope key when accessing a service using methods like **`getByClass`** or **`getByName`**. Each scope creates a completely independent instance, even though they all use the same underlying service definition.

So in this case:

* User A's chat runs in scope `"userA"`
* User B's chat runs in scope `"userB"`

Both use the same service logic, but their internal state stays isolated — no cross-contamination.

```javascript
const ChatService = Service({}, {
    userId: val(-1),
    messages: val([]),
    //
    initByUserId: func(),
    getMessages: func(),
});

const container = Container();

const chatForUserA = container.getByClass(ChatService, 'userA');
const chatForUserB = container.getByClass(ChatService, 'userB');

expect.not.toEqual(
    chatForUserA,
    chatForUserB,
)


await Promise.all([
    chatForUserA.initByUserId(32),
    chatForUserB.initByUserId(45),
]);
await Promise.all([
    chatForUserA.getMessages(),
    chatForUserB.getMessages(),
]);
expect.not.toEqual(
    chatForUserA.userId,
    chatForUserB.userId,
)
```

{% hint style="success" %}
In React apps, scoping integrates seamlessly. You can pass the **scope** option directly to hooks like `useService` and `useSignal`, giving each UI section its own isolated service instance.
{% endhint %}

```jsx
function ChatView({ userId }) {
    const [chat] = useSignal([ChatService], userId);
    
    useEffect(() => {
        chat.initByUserId(userId)
            .then(() => chat.getMessages());
    }, []);
    
    return (
        <ul>
            {chat.messages.map((message) => <li>{message.text}</li>)}
        </ul>
    );
}
```
