# Getting Started

BeatleJS is designed to make state management simple, reactive, and modular. This section will guide you through the installation process, setting up your project and first service, and integrating it with a React application.

### Installation

Run the following command to install the core BeatleJS library:

```bash
npm install beatlejs
```

### Set Up the Container and Provider

To use services throughout your application, wrap your app in a `BeatleProvider`

```tsx
import { Container } from 'beatlejs';

// imports are seperated and can be used when needed
import { BeatleProvider } from 'beatlejs/integrations/react';

import { createRoot } from 'react-dom/client';
import { App } from './App';

const container = Container();

createRoot(document.getElementById('root')!).render(
  <BeatleProvider container={container}>
    <App />
  </BeatleProvider>,
);
```

* The `BeatleProvider` uses React's context but **does not** cause unnecessary re-renders across your app. State updates in services only re-render components that directly use them.

### Define a Service

Create a new service using BeatleJS. A service encapsulates logic and state for your application, services must be defined within the module scope.

```typescript
import { Service, val, func } from 'beatlejs';
import { signal } from 'beatlejs/decorators/signal';

const MyService = Service(
  { identifier: 'MyService' },
  {
    title:val("My First Service"), // to initialize values, use val decorator 
    count: signal(val(0)), // decorate values that have side effect with signal
    increment: func(increment), // to initialize functions, use func decorator
  },
);
// to access service instance within 
function increment(this: typeof MyService) {
  this.count += 1;
}
```

* Each service must have a unique identifier. Otherwise, you'll receive a 'DuplicateError'.
* DuplicateErrors are expected during HMR sessions when services are updated, and the hot reload module tries to replace the existing service with the new version. In this case, you should reload the app for the services to load correctly.

### Use the Service in Component

Use the `useSignal` hook  to access and interact with the service in a React component:

```tsx
import { useSignal } from 'beatlejs/plugins/signal';
import { MyService } from './services/MyService';

export function MyComponent() {
  const [svc] = useSignal([MyService]);

  return (
  <>
    <h1> {svc.title} </h1> {/* Display title's value */}
    <button onClick={() => svc.increment()}>
      Counter: {svc.count} {/* Display the signal's value */}
    </button>
  </>
  );
}

```

