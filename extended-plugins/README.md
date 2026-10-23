# Extended Plugins

Primitive decorators like `val` and `func` serve as the foundation for defining service properties and methods. But BeatleJS’s real power comes from its **extensibility** — you can extend these primitives using plugins to create new decorators with custom metadata and behaviors.

* The **`val`** primitive can be extended with decorators such as `signal`, `persist`, and others that enhance state handling.
* The `func` primitive can be extended with decorators like `timer`, `retry`, and similar, adding advanced control to method execution.

Each extended decorator attaches specific metadata to service properties or methods, enabling you to tailor service behavior precisely to your application's needs.

We will explain each plugin and decorator in the chapters ahead, showing how they work with practical examples to help you understand their usage and benefits.
