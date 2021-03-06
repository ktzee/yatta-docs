---
title: "Resource Management"
---

Yatta provides specialized infrastructure for managing resources, including their initialization and finalization, with guaranteed ordering and error handling.

This feature is called **context managers** and it allows for generic, extensible management of local contexts. Context managers are used in conjunction with the [`with` expression](syntax.md#with-expression).

## Context Managers
Context managers provide the necessary gear for dealing with resource management is a structured way, that can be used together with the [`with` expression](syntax.md#with-expression).
They provide a way for Yatta to initialize and finalize resources. Resources may be explicitly or implicitly named direct access within the scope of the `with` expression.

Module [`context\Local`](stdlib/context.md/local) provides functions for implementing custom context managers. Builtin modules, such as [STM](stdlib/stm.md) or [File](stdlib/file.md) implement context managers as well.

Context managers store their data in a local dictionary, so that they can be used from any threads (this is transparent for programmers, who need not to be aware of this actually).

Context manager is a quadruple of `(name, enter, leave, data)`, where:
* `name` is the default name of the alias, used as a key in the local context dictionary.
* `enter` is a function taking context manager tuple as its argument, returning just the `data` portion.
* `leave` is a function taking context manager tuple as its argument, and its return value is ignored. Its purpose is handling side finalization effects only.
* `data` is the actual data, such as transaction object, file object or whatever other resource data to be managed by the context manager.

### Lifecycle of Context Managers
Context manager is executed in this order:
* the context manager tuple is passed to `enter` function
* result of the `enter` function is stored in the local context dictionary
* body expression is executed
* `leave` function is executed with the using a context manager tuple containing the result of `enter` function as its only argument
* if body expression raises an exception, the `leave` function is still called regardless

### Handling errors
Whether an exception is raised or not within the context manager execution is not important for the its lifecycle. Context managers ensure that proper initialization and finalization of resources always takes place and for this reason they are the recommended way for handling this sort of things.
