The `meta.py` file provides general-purpose utilities for meta-programming in Python, primarily focusing on decorators for dynamic behavior and custom iterator classes.

**Core Functionality and Structure:**

1.  **Exception Handling:**
    *   `MetaError`: A custom exception class used for errors encountered within the meta-programming utility code.

2.  **Decorators:**
    *   `decorate_all_methods(decorator)`: A higher-order decorator that takes another decorator as an argument. When applied to a class, it decorates *all* methods of that class (except `__init__`) with the provided `decorator`.
    *   `decorate_all_methods_and_properties(method_decorator, property_decorator)`: Similar to `decorate_all_methods`, but it applies `method_decorator` to all methods and `property_decorator` to all properties (excluding `__init__`). The snippet for this function is truncated, but its intent is clear from the name and parameters.
    *   `just_in_time_method(func)`: A method decorator designed for "just-in-time" (JIT) object loading or construction. It redirects calls to the decorated method to an equivalent method on an internal `item` attribute. This `item` is expected to be `None` initially and is loaded on demand using a `factory` and `key` attributes on the instance. This pattern allows for lazy loading of expensive objects. The decorated class must define `item`, `factory`, and `key` instance variables.
    *   `just_in_time_property(name, prop)`: A decorator for properties, analogous to `just_in_time_method`. It enables JIT loading of the `item` when the decorated property is accessed.

3.  **Iterators:**
    *   `PeekableIterator`: A wrapper for any iterable that allows peeking at the next item without consuming it. It provides a `peek()` method.
    *   `AutoApplyIterator`: Extends `PeekableIterator`. It allows a specified `on_next` function to be applied to the current and previous elements whenever the iterator advances. This is useful for tasks like checking sorting order or other constraints during iteration.

4.  **Unit Tests:**
    *   `TestMeta`: A `unittest.TestCase` class containing tests for the meta-programming utilities.
    *   `test_just_in_time_construction()`: Specifically tests the `just_in_time_method` decorator. It sets up a `Record` class, a `Factory` to retrieve `Record` instances, and then defines a `B` class that inherits from `Record` and uses `@decorate_all_methods(just_in_time_method)` to lazily load `Record` instances. The test asserts that methods on `B` correctly delegate to the underlying `Record` instance after it's loaded just-in-time.

**Important Details:**

*   **Licensing:** The file is licensed under the GNU General Public License, Version 3 or any later version.
*   **`inspect` Module:** The `inspect` module is heavily used to introspect classes and their members (methods, properties) for the decorator implementations.
*   **Decorator Application:** The `decorate_all_methods` and `decorate_all_methods_and_properties` decorators are powerful tools for applying cross-cutting concerns (like JIT loading) to all relevant members of a class with minimal boilerplate.
*   **Just-In-Time Mechanism:** The JIT decorators (`just_in_time_method`, `just_in_time_property`) are designed for scenarios where an object is expensive to create or load, and its methods/properties are not always needed immediately. By delegating to an `item` attribute that's loaded from a `factory` using a `key`, they provide an efficient lazy-loading mechanism.
*   **Iterator Design:** `PeekableIterator` and `AutoApplyIterator` offer enhanced control and functionality over standard Python iterators, particularly for scenarios requiring foresight or side effects during iteration.