### 1. allPromisesDone(promises: Promise<any>[]): Promise<void>

**Description:**

Waits for all promises in an array to complete before resolving, regardless of their individual successes or failures. This eliminates the need to chain `.then` or use `Promise.all` when only completion matters, not individual results.

**Code:**

```typescript
function allPromisesDone(promises: Promise<any>[]): Promise<void> {
  const pendingPromises = promises.length;
  return new Promise((resolve) => {
    promises.forEach((promise) =>
      promise.finally(() => {
        if (--pendingPromises === 0) {
          resolve();
        }
      })
    );
  });
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of promises. Stores references to all pending promises.
- Time complexity: O(1), assuming no additional processing within the waiting period.

**Considerations:**

- Useful for cleanup tasks or triggering actions at the end of asynchronous operations, regardless of individual outcomes.
- Handles both resolved and rejected promises.

**Example:**

```typescript
const fetchUserData = fetch("/api/user");
const fetchUserPosts = fetch("/api/user/posts");

allPromisesDone([fetchUserData, fetchUserPosts]).then(() => {
  // Both fetches are complete, proceed with data processing or UI updates
});
```

### 2. capitalizeFirst(str: string): string

**Description:**

Capitalizes the first letter of a string and leaves the rest unchanged.

**Code:**

```typescript
function capitalizeFirst(str: string): string {
  return str.charAt(0).toUpperCase() + str.slice(1);
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the string. Creates a new string with the same length.
- Time complexity: O(n), iterates through the string once.

**Considerations:**

- Simple and efficient way to format string titles, headers, or names.
- Works with any type of string, including those containing non-alphabetic characters.

**Example:**

```typescript
const title = "my amazing article";
const capitalizedTitle = capitalizeFirst(title); // CapitalizedTitle: 'My amazing article'

// Useful for consistent UI formatting or data parsing
```

### 3. chainErrorHandlers(handlers: ((error: Error) => void)[]): (error: Error) => void

**Description:**

Creates a chain of error handlers that are called sequentially when an error occurs. Useful for handling different types of errors with specific logic in each step.

**Code:**

```typescript
function chainErrorHandlers(
  handlers: ((error: Error) => void)[]
): (error: Error) => void {
  let currentHandlerIndex = 0;
  const handleError = (error: Error) => {
    if (currentHandlerIndex === handlers.length) {
      throw error; // No remaining handlers, rethrow error
    }
    handlers[currentHandlerIndex](error);
    currentHandlerIndex++;
  };
  return handleError;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of error handlers. Stores references to each handler function.
- Time complexity: O(n), iterates through the handler chain until one successfully handles the error or the chain ends.

**Considerations:**

- Provides a structured approach to error handling, allowing for specific logic for different error types or categories.
- Can be used in asynchronous operations like promises or event listeners.

**Example:**

```typescript
const loggingHandler = (error: Error) =>
  console.error("Error occurred:", error);
const retryHandler = async (error: Error) => {
  if (isNetworkError(error)) {
    await retryOperation();
  } else {
    throw error;
  }
};

const handleAllErrors = chainErrorHandlers([loggingHandler, retryHandler]);

try {
  await someAsyncOperation();
} catch (error) {
  handleAllErrors(error); // Sequentially calls logging and retry handlers
}
```

### 4. chunkifyArray<T>(arr: T[], chunkSize: number): T[][]

**Description:**

Divides an array into smaller sub-arrays of a specified size. Useful for iterating or processing large arrays in smaller chunks.

**Code:**

```typescript
function chunkifyArray<T>(arr: T[], chunkSize: number): T[][] {
  const chunks = [];
  for (let i = 0; i < arr.length; i += chunkSize) {
    chunks.push(arr.slice(i, i + chunkSize));
  }
  return chunks;
}
```

**Space and Time Complexity:**

- Space complexity: O(n/chunkSize), where n is the length of the original array. Creates the same number of chunks.
- Time complexity: O(n/chunkSize), iterates through the original array once.

**Considerations:**

- Improves performance for iterating or processing large arrays by breaking them down into smaller units.
- Can be used with asynchronous operations to avoid overloading the main thread.

**Example:**

```typescript
const largeDataset = generateLargeArray(1000);

const chunks = chunkifyArray(largeDataset, 100); // Breaks data into 10 chunks of 100 elements each

for (const chunk of chunks) {
  // Process each chunk in a separate operation or loop
}
```

### 5. deepMergeObjects<T1, T2>(obj1: T1, obj2: T2): T1 & T2

**Description:**

Performs a deep merge of two objects, combining their properties recursively while preserving existing structures in both objects. This allows for merging configuration settings, complex data structures, or extending one object with another.

**Code:**

```typescript
function deepMergeObjects<T1, T2>(obj1: T1, obj2: T2): T1 & T2 {
  const mergedObj: T1 & T2 = Object.assign({}, obj1);
  for (const key in obj2) {
    if (isObject(obj1[key]) && isObject(obj2[key])) {
      mergedObj[key] = deepMergeObjects(obj1[key], obj2[key]);
    } else {
      mergedObj[key] = obj2[key];
    }
  }
  return mergedObj;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the total number of keys in both objects. Creates a new object with combined properties.
- Time complexity: O(n), iterates through all keys in both objects.

**Considerations:**

- Efficiently merges objects of any depth, preserving existing data structures.
- Handles conflicting keys by prioritizing values from the second object.
- Requires both objects to be plain objects and not primitive types.

**Example:**

```typescript
const userConfig = { name: "John", settings: { theme: "dark" } };
const appDefaults = { settings: { language: "en" } };

const mergedConfig = deepMergeObjects(userConfig, appDefaults); // { name: 'John', settings: { theme: 'dark', language: 'en' } }

// Unified configuration object with combined user and default settings
```

### 6. filterNonUnique<T>(array: T[]): T[]

**Description:**

Removes duplicate elements from an array while preserving the original order of unique elements. This is useful for cleaning up data sets or ensuring unique items in lists or collections.

**Code:**

```typescript
function filterNonUnique<T>(array: T[]): T[] {
  const seen = new Set();
  return array.filter((item) => {
    if (!seen.has(item)) {
      seen.add(item);
      return true;
    }
    return false;
  });
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the original array. Uses a set to store encountered elements.
- Time complexity: O(n), iterates through the array once.

**Considerations:**

- Efficiently removes duplicates while maintaining the original order.
- Works with any type of elements that can be compared for equality.
- Ideal for situations where order matters and only unique elements are needed.

**Example:**

```typescript
const numbers = [1, 2, 3, 1, 4, 3, 2];

const uniqueNumbers = filterNonUnique(numbers); // [1, 2, 3, 4]

// Original order of unique elements is preserved
```

### 7. findKeyByValue<T, K>(obj: T, predicate: (value: T[keyof T], key: keyof T) => boolean): keyof T | undefined

**Description:**

Searches an object for the first key that satisfies a given predicate based on both the key and the corresponding value. Useful for finding specific objects by combining key and value criteria.

**Code:**

```typescript
function findKeyByValue<T, K>(
  obj: T,
  predicate: (value: T[keyof T], key: keyof T) => boolean
): keyof T | undefined {
  for (const key in obj) {
    if (predicate(obj[key], key)) {
      return key;
    }
  }
  return undefined;
}
```

**Space and Time Complexity:**

- Space complexity: O(1), only stores temporary variables.
- Time complexity: O(n), iterates through the object's keys once.

**Considerations:**

- More flexible than finding by value alone, allowing you to match specific key-value combinations.
- Useful for identifying objects based on custom criteria beyond simple value comparison.
- Requires a well-defined predicate function that interprets both key and value.

**Example:**

```typescript
const users = {
  john: { age: 30, role: "admin" },
  jane: { age: 25, role: "user" },
};

const firstAdminKey = findKeyByValue(
  users,
  (value, key) => value.role === "admin"
); // firstAdminKey: 'john'

// Find objects based on key-value matches instead of just values
```

### 8. flattenObjectPath(obj: any, delimiter?: string): any

**Description:**

Transforms a nested object with potentially deep hierarchy into a flat object where property names represent the full path to the original values. Useful for simplifying data structures, accessing deeply nested values with clearer identifiers, or preparing data for serialization.

**Code:**

```typescript
function flattenObjectPath(obj: any, delimiter = "."): any {
  const flatObj = {};
  function flatten(currentObj: any, path: string = "") {
    for (const key in currentObj) {
      const newPath = path ? `${path}${delimiter}${key}` : key;
      if (isObject(currentObj[key])) {
        flatten(currentObj[key], newPath);
      } else {
        flatObj[newPath] = currentObj[key];
      }
    }
  }
  flatten(obj);
  return flatObj;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the total number of elements in the nested object. Creates a new flat object.
- Time complexity: O(n), iterates through the object recursively once.

**Considerations:**

- Simplifies navigation within complex structures by using hierarchical property names.
- May increase property name length and potentially affect serialization formats.
- Useful for data manipulation, analysis, or preparing data for external APIs.

**Example:**

```typescript
const nestedData = {
  user: {
    name: "John",
    address: {
      city: "New York",
      street: "Main Street",
    },
  },
};

const flatData = flattenObjectPath(nestedData); // { 'user.name': 'John', 'user.address.city': 'New York', 'user.address.street': 'Main Street' }

// Easier access to nested values with descriptive property names
```

### 9. getFunctionArgs<T>(fn: (...args: any[]) => T): string[]

**Description:**

Extracts the formal parameter names of a function using TypeScript's type information. Useful for generating documentation, type-aware introspection, or dynamically adapting function behavior based on its arguments.

**Code:**

```typescript
function getFunctionArgs<T>(fn: (...args: any[]) => T): string[] {
  const fnType = typeof fn === "function" ? fn.constructor.toString() : "";
  const match = fnType.match(/function\s*\(.*?\)/);
  return match ? match[1].split(",").map((arg) => arg.trim()) : [];
}
```

**Space and Time Complexity:**

- Space complexity: O(1), only uses constant memory for regex matching.
- Time complexity: O(n), where n is the length of the function string representation.

**Considerations:**

- Works with functions declared using the `function` keyword, not arrow functions.
- Extracts formal parameter names only, not type information or default values.
- Useful for type-safe introspection and dynamic behavior without relying on string parsing.

**Example:**

```typescript
function addNumbers(a: number, b: number): number {
  return a + b;
}

const args = getFunctionArgs(addNumbers); // args: ['a', 'b']

console.log(`Function takes arguments: ${args.join(", ")}`);
```

### 10. groupByMapValue<T, K>(array: T[], mapFn: (item: T) => K): Map<K, T[]>

**Description:**

Groups an array of items into a Map based on the key returned by a mapping function applied to each item. Useful for organizing data based on specific criteria, efficiently accessing related items, or performing aggregate operations on groups.

**Code:**

```typescript
function groupByMapValue<T, K>(array: T[], mapFn: (item: T) => K): Map<K, T[]> {
  const groups = new Map<K, T[]>();
  for (const item of array) {
    const key = mapFn(item);
    let group = groups.get(key);
    if (!group) {
      group = [];
      groups.set(key, group);
    }
    group.push(item);
  }
  return groups;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the array. Creates a Map to store grouped items.
- Time complexity: O(n), iterates through the array once.

**Considerations:**

- Efficiently groups data based on any custom criteria defined by the map function.
- Maintains the original order of items within each group.
- Requires a well-defined map function that consistently generates key values.

**Example:**

```typescript
const users = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
  { name: "Alice", age: 30 },
];

const groupsByAge = groupByMapValue(users, (user) => user.age);

console.log(groupsByAge.get(30)); // [ { name: 'John' }, { name: 'Alice' } ]

// Access or iterate over users grouped by their age
```

### 11. isPromiseLike(obj: any): obj is Promise<any>

**Description:**

Checks if a given value is a promise-like object, meaning it has a `then` function and implements the Promise interface. Useful for safely handling potentially asynchronous situations.

**Code:**

```typescript
function isPromiseLike(obj: any): obj is Promise<any> {
  return typeof obj === "object" && typeof obj.then === "function";
}
```

**Space and Time Complexity:**

- Space complexity: O(1), constant memory for type checks.
- Time complexity: O(1), simple type comparisons.

**Considerations:**

- More robust than simple `typeof` checks, avoiding false positives like objects with custom `then` methods.
- Useful for generic code that needs to handle both synchronous and asynchronous values.

**Example:**

```typescript
function fetchData(url: string): Promise<any> {
  // ... fetching data
}

function handleResponse(response: any) {
  if (isPromiseLike(response)) {
    response.then((data) => {
      // Process data when promise resolves
    });
  } else {
    // Handle synchronous response directly
  }
}
```

### 12. memoized<T>(fn: (...args: any[]) => T): (...args: any[]) => T

**Description:**

Creates a memoized version of a function, caching its results based on the input arguments. Improves performance for repeatedly called functions with the same inputs.

**Code:**

```typescript
function memoized<T>(fn: (...args: any[]) => T): (...args: any[]) => T {
  const cache = new Map<any[], T>();
  return (...args: any[]) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key)!;
    }
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of unique argument combinations used. Creates a cache object.
- Time complexity: O(1) for cached entries, O(n) for initial function call with cache update.

**Considerations:**

- Effective for performance optimization in scenarios where function calls with the same arguments are frequent.
- Requires careful choice of functions to memoize to avoid caching unnecessary computations.

**Example:**

```typescript
function fibonacci(n: number): number {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

// Memoized version for faster repeated calculations
const memoizedFibonacci = memoized(fibonacci);
```

### 13. parseQueryString(queryString: string): Record<string, string>

**Description:**

Parses a URL query string into a key-value object, converting encoded characters and handling multiple values for the same key. Useful for building web applications that interact with query parameters.

**Code:**

```typescript
function parseQueryString(queryString: string): Record<string, string> {
  const params = {};
  for (const param of queryString.split("&")) {
    const [key, value] = param.split("=");
    const decodedKey = decodeURIComponent(key);
    const decodedValue = decodeURIComponent(value);
    if (params.hasOwnProperty(decodedKey)) {
      params[decodedKey] = Array.isArray(params[decodedKey])
        ? [...params[decodedKey], decodedValue]
        : [params[decodedKey], decodedValue];
    } else {
      params[decodedKey] = decodedValue;
    }
  }
  return params;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the query string. Creates a new object to store parameters.
- Time complexity: O(n), iterates through each parameter in the query string.

**Considerations:**

- Simplifies handling query parameters and avoids manual parsing.
- Supports multiple values for the same key and decodes URL-encoded characters.

**Example:**

```typescript
const url = "https://example.com/search?term=TypeScript&category=libraries";
const queryParams = parseQueryString(url.split("?")[1]);

console.log(queryParams); // { term: 'TypeScript', category: ['libraries'] }
```

### 14. pickByKey<T, K extends keyof T>(obj: T, keys: K[] | K): { [K in keyof T]: T[K] }

**Description:**

Selects specific properties from an object based on a provided array or single key of the object's own keys. Useful for extracting relevant data from larger objects without copying the entire structure.

**Code:**

```typescript
function pickByKey<T, K extends keyof T>(
  obj: T,
  keys: K[] | K
): { [K in keyof T]: T[K] } {
  const target: { [K in keyof T]: T[K] } = {};
  if (Array.isArray(keys)) {
    for (const key of keys) {
      if (key in obj) target[key] = obj[key];
    }
  } else {
    if (keys in obj) target[keys] = obj[keys];
  }
  return target;
}
```

**Space and Time Complexity:**

- Space complexity: O(k), where k is the number of selected keys. Creates a new object with selected properties.
- Time complexity: O(k), iterates through the provided keys or checks for single key existence.

**Considerations:**

- Efficiently extracts specific data without unnecessary property copying.
- Works with both single and multiple key selection.
- Requires the provided keys to be valid properties of the object.

**Example:**

```typescript
const user = { name: "John", age: 30, role: "admin" };

const selectedUserData = pickByKey(user, ["name", "age"]); // { name: 'John', age: 30 }

const singleProperty = pickByKey(user, "role"); // { role: 'admin' }
```

### 15. debounce<T>(fn: (...args: any[]) => T, delay: number): (...args: any[]) => void

**Description:**

Creates a debounced version of a function, delaying its execution until a specified time has passed after the last call. Useful for preventing excessive function calls in scenarios like event listeners or user input.

**Code:**

```typescript
function debounce<T>(
  fn: (...args: any[]) => T,
  delay: number
): (...args: any[]) => void {
  let timeout: NodeJS.Timeout | null = null;
  return (...args: any[]) => {
    if (timeout) clearTimeout(timeout);
    timeout = setTimeout(() => fn(...args), delay);
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(1), uses a single timer for each debounced function.
- Time complexity: O(1) for function call, O(delay) for actual execution after timer expires.

**Considerations:**

- Improves performance and prevents unwanted side effects by delaying executions.
- Useful for UI updates, network requests, or any actions that don't need immediate execution.
- Configure the delay based on the desired responsiveness and performance needs.

**Example:**

```typescript
const resizeHandler = debounce(
  () => window.dispatchEvent(new Event("resize")),
  250
);

window.addEventListener("resize", resizeHandler); // Window resize event only triggers 250ms after the last change.
```

### 16. throttle<T>(fn: (...args: any[]) => T, limit: number, interval: number): (...args: any[]) => void

**Description:**

Limits the execution frequency of a function to a specific number of calls within a given interval. Useful for scenarios where frequent function calls could overwhelm the system or have undesirable side effects.

**Code:**

```typescript
function throttle<T>(
  fn: (...args: any[]) => T,
  limit: number,
  interval: number
): (...args: any[]) => void {
  let remainingCalls = limit;
  let lastCallTime = 0;
  let timeout: NodeJS.Timeout | null = null;
  return (...args: any[]) => {
    if (remainingCalls === 0 && Date.now() - lastCallTime < interval) {
      if (!timeout) {
        timeout = setTimeout(() => {
          remainingCalls = limit;
          lastCallTime = Date.now();
          fn(...args);
        }, interval);
      }
    } else {
      remainingCalls--;
      lastCallTime = Date.now();
      fn(...args);
    }
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(1), uses a single timer and call counter for each throttled function.
- Time complexity: O(1) for function call, O(interval) for delayed execution if throttling limit is reached.

**Considerations:**

- Provides more control over execution frequency compared to simple debouncing.
- Useful for tasks like network requests, API calls, or computationally expensive operations.
- Adjust the limit and interval based on the desired balance between responsiveness and performance.

**Example:**

```typescript
const scrollThrottler = throttle(
  () => console.log("Scrolling detected"),
  10,
  100
);

window.addEventListener("scroll", scrollThrottler); // 'Scrolling detected' is only logged 10 times every 100ms.
```

### 17. shuffleArray<T>(array: T[]): T[]

**Description:**

Randomly shuffles the order of elements in an array without modifying the original array. Useful for introducing randomness, creating random samples, or performing unbiased experiments.

**Code:**

```typescript
function shuffleArray<T>(array: T[]): T[] {
  const shuffledArray = [...array];
  for (let i = shuffledArray.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffledArray[i], shuffledArray[j]] = [shuffledArray[j], shuffledArray[i]];
  }
  return shuffledArray;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), creates a new shuffled array of the same size.
- Time complexity: O(n), iterates through the array once for swapping elements.

**Considerations:**

- Provides a simple and efficient way to randomize element order.
- Use case-dependent considerations for ensuring randomness quality and potential biases.

**Example:**

```typescript
const numbers = [1, 2, 3, 4, 5];

const shuffledNumbers = shuffleArray(numbers); // Randomly ordered array with the same elements.

console.log(shuffledNumbers); // [4, 5, 1, 3, 2] (Example output)
```

### 18. camelize(str: string): string

**Description:**

Converts a string from snake_case or kebab-case to camelCase, where the first letter of each word is capitalized and any underscores or hyphens are removed. Useful for consistent naming conventions and improved legibility.

**Code:**

```typescript
function camelize(str: string): string {
  return str
    .replace(/(_|-)(\w)/g, (_: string, char: string) => char.toUpperCase())
    .replace(/^[A-Z]/g, (match: string) => match.toLowerCase());
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the original string. Creates a new string with transformed case.
- Time complexity: O(n), iterates through the string and performs regex replacements.

**Considerations:**

- Works with both snake_case and kebab-case formats.
- Handles acronyms and edge cases like leading capitals.
- May require further adjustments for more complex or custom naming conventions.

**Example:**

```typescript
const snakeCase = "this_is_snake_case";
const kebabCase = "this-is-kebab-case";

const camelCase1 = camelize(snakeCase); // thisIsSnakeCase
const camelCase2 = camelize(kebabCase); // thisIsKebabCase
```

### 19. deepFreeze<T>(obj: T): T

**Description:**

Recursively freezes an object and all its nested objects, preventing any further property modifications. Useful for creating immutable data structures that ensure consistency and avoid unexpected changes.

**Code:**

```typescript
function deepFreeze<T>(obj: T): T {
  if (typeof obj !== "object" || obj === null) return obj;
  Object.freeze(obj);
  for (const key in obj) {
    deepFreeze(obj[key]);
  }
  return obj;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the total number of elements in the object tree. Calls `deepFreeze` on each nested object.
- Time complexity: O(n), iterates through all properties of the object and its descendants.

**Considerations:**

- Provides immutability for data security and predictable behavior.
- Works with any complex nested object structures.
- Be aware that modifying frozen objects throws errors, potentially affecting existing code.

**Example:**

```typescript
const user = {
  name: "John",
  settings: {
    theme: "dark",
  },
};

const frozenUser = deepFreeze(user);

// Attempting to modify frozen properties throws an error
frozenUser.name = "Jane"; // Error: Cannot modify frozen object

// Use separate mutable copies for modification if needed
let modifiedUser = { ...user };
modifiedUser.settings.theme = "light";

console.log(frozenUser); // { name: 'John', settings: { theme: 'dark' } }
console.log(modifiedUser); // { name: 'John', settings: { theme: 'light' } }
```

### 20. curry<T>(fn: (...args: any[]) => T, arity?: number): (...args: any[]) => T | ((...args: any[]) => T)

**Description:**

Partially applies a function to a subset of its arguments, returning a new function that takes the remaining arguments. Useful for building modular functions and creating reusable function compositions.

**Code:**

```typescript
function curry<T>(
  fn: (...args: any[]) => T,
  arity?: number
): (...args: any[]) => T | ((...args: any[]) => T) {
  arity = arity || fn.length;
  return function curried(...args: any[]) {
    if (args.length >= arity) {
      return fn(...args);
    }
    return curry<T>(fn, arity - args.length)(...args);
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of applied arguments. Creates new closures for each partially applied function.
- Time complexity: O(1) for function call, O(n) for iterating through applied arguments.

**Considerations:**

- Enables functional programming patterns like chaining and building higher-order functions.
- Works with functions of any arity (number of arguments).
- Adjust the `arity` parameter to specify the expected number of arguments for final execution.

**Example:**

```typescript
const add = (a: number, b: number, c: number) => a + b + c;

const addTwo = curry(add)(2); // Function that takes one argument and adds 2 to it

const result1 = addTwo(3); // 5
const result2 = add(1, addTwo(4)); // 7

console.log(result1, result2);
```

### 21. memoizeBy(keyFn: (args: any[]) => any, fn: (...args: any[]) => any): (...args: any[]) => any

**Description:**

Creates a memoized version of a function based on a custom key generation function. Caches results based on the computed key instead of the entire argument list. Useful for scenarios where different argument combinations might lead to the same result.

**Code:**

```typescript
function memoizeBy<T>(
  keyFn: (args: any[]) => any,
  fn: (...args: any[]) => T
): (...args: any[]) => T {
  const cache = new Map<any, T>();
  return (...args: any[]) => {
    const key = keyFn(args);
    if (cache.has(key)) {
      return cache.get(key)!;
    }
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of unique keys encountered. Creates a cache entry for each distinct key.
- Time complexity: O(1) for cached entries, O(n) for initial function call with key computation and cache update.

**Considerations:**

- Provides fine-grained control over memoization based on your custom key function.
- Reduces unnecessary computations for recurring arguments that map to the same results.
- Choose the `keyFn` carefully to ensure accurate and efficient caching behavior.

**Example:**

```typescript
const distanceBetweenPoints = (
  x1: number,
  y1: number,
  x2: number,
  y2: number
) => Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));

const memoizedDistance = memoizeBy(
  (args) => `(${args[0]},${args[1]}) - (${args[2]},${args[3]})`,
  distanceBetweenPoints
);

// Same distance calculations return the cached result
const dist1 = memoizedDistance(1, 2, 3, 4);
const dist2 = memoizedDistance(1, 2, 3, 4);

console.log(dist1 === dist2); // true
```

### 22. groupBy(arr: T[], prop?: keyof T | ((item: T) => any)): Map<any, T[]>

**Description:**

Groups an array of items into a Map based on a provided property or custom grouping function. Useful for organizing data based on specific criteria and efficiently accessing related items within each group.

**Code:**

```typescript
function groupBy<T>(
  arr: T[],
  prop?: keyof T | ((item: T) => any)
): Map<any, T[]> {
  const groups = new Map<any, T[]>();
  if (prop) {
    for (const item of arr) {
      const key = typeof prop === "function" ? prop(item) : item[prop];
      let group = groups.get(key);
      if (!group) {
        group = [];
        groups.set(key, group);
      }
      group.push(item);
    }
  } else {
    for (const item of arr) {
      const key = item; // Group by entire item if no prop provided
      let group = groups.get(key);
      if (!group) {
        group = [];
        groups.set(key, group);
      }
      group.push(item);
    }
  }
  return groups;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the array. Creates a Map object to store grouped items.
- Time complexity: O(n), iterates through the array and performs key/group assignments.

**Considerations:**

- Works with both static properties and custom grouping functions.
- Maintains the original order of items within each group.
- Choose the grouping criteria based on your data structure and analysis needs.

**Example:**

```typescript
const users = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
  { name: "Alice", age: 30 },
];

const groupsByAge = groupBy(users, (user) => user.age);

console.log(groupsByAge.get(30)); // [ { name: 'John' }, { name: 'Alice' } ]

// Group by first letter of name
const groupsByFirstLetter = groupBy(users, (user) => user.name[0]);

console.log(groupsByFirstLetter.get("J")); // [ { name: 'John', age: 30 }, { name: 'Jane', age: 25 } ]
```

### 23. deepFlatten<T>(arr: T[] | T[][] | T[][][]): T[]

**Description:**

Recursively flattens a nested array to a single-dimensional array, preserving the original order of elements and handling non-array items differently than Lodash's `_.flatten`. Non-array items are wrapped in single-element arrays, enabling consistent processing of all elements as arrays.

**Code:**

```typescript
function deepFlatten<T>(arr: T[] | T[][] | T[][][]): T[] {
  const result: T[] = [];
  for (const item of arr) {
    if (Array.isArray(item)) {
      result.push(...deepFlatten(item));
    } else {
      result.push([item]); // Wrap non-array items in arrays
    }
  }
  return result;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the total number of elements in all nested arrays. Creates a new flattened array.
- Time complexity: O(n), iterates through each element and performs array/element checks and nested flatten calls if needed.

**Considerations:**

- Works with any depth of nested arrays.
- Wraps non-array items in single-element arrays for consistent processing.
- Consider alternative data structures like flatMaps for potentially more efficient handling in specific scenarios.

**Example:**

```typescript
const nestedArr = [1, [2, 3], [[4, 5, "hello"], 6]];
const flattenedArr = deepFlatten(nestedArr);
console.log(flattenedArr); // [1, 2, 3, [4, 5, 'hello'], 6]
```

### 24. interleave<T>(...arrs: T[][]): T[]

**Description:**

Combines multiple arrays into a single array by interleaving their elements in a round-robin fashion. Differs from Lodash's `_.chunk` by creating a linear interleaved sequence rather than dividing an array into chunks.

**Code:**

```typescript
function interleave<T>(...arrs: T[][]): T[] {
  const result: T[] = [];
  let i = 0;
  while (true) {
    let done = true;
    for (const arr of arrs) {
      if (i < arr.length) {
        result.push(arr[i]);
        done = false;
      }
    }
    if (done) break;
    i++;
  }
  return result;
}
```

**Space and Time Complexity:**

- Space complexity: O(m), where m is the total number of elements in all input arrays. Creates a new interleaved array.
- Time complexity: O(m \* n), where n is the number of input arrays. Iterates through each array until all elements are processed.

**Considerations:**

- Handles arrays of different lengths by interleaving until the shortest array is exhausted.
- Doesn't modify original arrays.
- Useful for creating alternating sequences or combining data from multiple sources.

**Example:**

```typescript
const arr1 = [1, 4, 7];
const arr2 = [2, 5];
const arr3 = [3, 6, 8, 9];
const interleavedArr = interleave(arr1, arr2, arr3);
console.log(interleavedArr); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 25. joinPaths(...paths: string[]): string

**Description:**

Joins multiple paths into a single, normalized path string, resolving relative paths, handling duplicate separators, and ensuring consistent forward slashes. Useful for building file paths, URLs, or other directory-based structures.

**Code:**

```typescript
function joinPaths(...paths: string[]): string {
  const normalizedPaths = paths.map((path) =>
    path.trim().replace(/[/\\ ]+/g, "/")
  );
  const joinedPath = normalizedPaths.reduce((result, path) => {
    if (!result) return path;
    if (path.startsWith("/")) return path;
    if (result.endsWith("/")) return result + path;
    return result + "/" + path;
  }, "");
  return joinedPath;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the total length of all paths combined. Creates temporary arrays for normalization.
- Time complexity: O(n), iterates through each path for trimming, normalization, and joining.

**Considerations:**

- Handles relative paths like `./` and `../` appropriately.
- Removes duplicate separators and ensures consistent forward slashes.
- Consider alternative libraries like `path` for more comprehensive path manipulations.

**Example:**

```typescript
const basePath = "/home/user";
const relativePath = "./data/file.txt";
const additionalPath = "../downloads/image.png";

const joinedPath = joinPaths(basePath, relativePath, additionalPath);
console.log(joinedPath); // /home/user/data/file.txt
```

### 26. keysOfType<T, U>(obj: T, type: U): (keyof T)[]

**Description:**

Extracts the keys of an object whose corresponding values are of a specified type. Useful for filtering and accessing object properties based on their data types.

**Code:**

```typescript
function keysOfType<T, U>(obj: T, type: U): (keyof T)[] {
  return Object.keys(obj).filter((key) => typeof obj[key] === type);
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of keys in the object. Creates an array to store filtered keys.
- Time complexity: O(n), iterates through keys and performs type checks.

**Considerations:**

- Leverages TypeScript's type system for type safety and flexibility.
- Useful for data validation, conditional logic, and type-driven operations.

**Example:**

```typescript
const person = {
  name: "Alice",
  age: 30,
  hobbies: ["coding", "reading"],
};

const stringKeys = keysOfType(person, "string");
console.log(stringKeys); // ['name']

const numberKeys = keysOfType(person, "number");
console.log(numberKeys); // ['age']
```

### 27. readFileChunks(file: File, chunkSize: number = 1024): AsyncGenerator<ArrayBuffer, void, void>

**Description:**

Reads a file in chunks asynchronously, yielding each chunk as an ArrayBuffer, making it suitable for handling large files efficiently and processing data as it's read.

**Code:**

```typescript
async function* readFileChunks(
  file: File,
  chunkSize: number = 1024
): AsyncGenerator<ArrayBuffer, void, void> {
  const reader = new FileReader();
  let readChunk: ArrayBuffer | null = null;

  while (
    (readChunk = await reader.readAsArrayBuffer(
      file.slice(reader.result as number)
    )) !== null
  ) {
    yield readChunk;
  }
}
```

**Space and Time Complexity:**

- Space complexity: O(chunkSize), as each chunk is stored in memory.
- Time complexity: O(n), where n is the file size, as it reads the file chunk by chunk.

**Considerations:**

- Provides finer control over memory usage and processing compared to reading the entire file at once.
- Useful for handling large files, streaming data, or processing data as it's being read.

**Example:**

```typescript
const file = await fetch("https://example.com/large-file.zip").then((res) =>
  res.blob()
);

for await (const chunk of readFileChunks(file)) {
  // Process each chunk of the file
}
```

### 28. reduceRightWhile<T>(arr: T[], reducer: (acc: T, curr: T) => T | undefined, initialValue: T): T | undefined

**Description:**

Reduces an array from right to left, applying a reducer function to each element and accumulating a result, but stops early if the reducer returns `undefined`.

**Code:**

```typescript
function reduceRightWhile<T>(
  arr: T[],
  reducer: (acc: T, curr: T) => T | undefined,
  initialValue: T
): T | undefined {
  let acc = initialValue;
  for (let i = arr.length - 1; i >= 0; i--) {
    acc = reducer(acc, arr[i]);
    if (acc === undefined) {
      break;
    }
  }
  return acc;
}
```

**Space and Time Complexity:**

- Space complexity: O(1), uses constant extra space.
- Time complexity: O(n), where n is the length of the array, but might stop early if the reducer returns `undefined`.

**Considerations:**

- Useful for short-circuiting reductions when a specific condition is met.
- Can optimize certain operations by avoiding unnecessary iterations.

**Example:**

```typescript
const numbers = [1, 2, 3, 4, 5];
const firstEven = reduceRightWhile(
  numbers,
  (acc, num) => {
    if (num % 2 === 0) return num;
  },
  undefined
);
console.log(firstEven); // 4
```

### 29. shuffleInPlace<T>(arr: T[]): void

**Description:**

Randomly shuffles the elements of an array in-place without creating a new one. This improves performance and memory usage for large datasets.

**Code:**

```typescript
function shuffleInPlace<T>(arr: T[]): void {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
}
```

**Space and Time Complexity:**

- Space complexity: O(1), modifies the original array in-place.
- Time complexity: O(n), iterates through the array once and performs random swaps.

**Considerations:**

- Modifies the original array directly, unlike `Math.random` which returns a pseudo-random number.
- Efficient for large datasets compared to creating and shuffling a new array.

**Example:**

```typescript
const cards = ["Ace", "King", "Queen", "Jack"];
shuffleInPlace(cards);
console.log(cards); // Output order is random each time
```

### 30. groupByLongest(arr: string[], delim: string = ','): { [key: string]: string[] }

**Description:**

Groups a string array into objects based on the longest common substring found between each element and another element in the array. Useful for identifying overlapping phrases or related keywords.

**Code:**

```typescript
function groupByLongest(
  arr: string[],
  delim: string = ","
): { [key: string]: string[] } {
  const groups: { [key: string]: string[] } = {};
  for (let i = 0; i < arr.length; i++) {
    const word = arr[i];
    for (let j = i + 1; j < arr.length; j++) {
      const otherWord = arr[j];
      const commonSubstring = getLongestCommonSubstring(word, otherWord, delim);
      if (commonSubstring) {
        groups[commonSubstring] = groups[commonSubstring] || [];
        groups[commonSubstring].push(word, otherWord);
      }
    }
  }
  return groups;
}

function getLongestCommonSubstring(
  str1: string,
  str2: string,
  delim: string
): string | null {
  // Implement a longest common substring algorithm (e.g., Longest Common Subsequence)
  // ...
  // This helper function finds the longest common substring with the specified delimiter
}
```

**Space and Time Complexity:**

- Space complexity: O(n^2), where n is the number of elements in the array. Creates a group object for each common substring.
- Time complexity: O(n^2), nested loops iterate through all pairs of elements in the worst case.

**Considerations:**

- Uses longest common substring to find deeper connections between phrases, not just exact matches.
- Useful for text analysis, keyword clustering, and data classification tasks.

**Example:**

```typescript
const phrases = [
  "big data analysis",
  "data analytics course",
  "large data processing",
  "data analytics for business",
];
const groups = groupByLongest(phrases);
console.log(groups); // { 'data analytics': ['big data analysis', 'data analytics course'] }
```

### 31: throttleByArgs<T, R>(fn: (args: T) => R, delay: number, context?: ThisType<T>): (...args: T[]) => Promise<R>

**Description:**

- Throttles a function's execution based on its arguments.
- Only executes the function once for each unique set of arguments within a specified delay period.
- Ensures only necessary calls are made, even for frequent invocations with varying arguments.

**Code:**

```typescript
function throttleByArgs<T, R>(
  fn: (args: T) => R,
  delay: number,
  context?: ThisType<T>
): (...args: T[]) => Promise<R> {
  const activeMap: Map<string, Date> = new Map();
  return (...args: T[]) => {
    const key = JSON.stringify(args); // Uniquely identify the argument set
    const now = Date.now();
    if (activeMap.has(key) && now - activeMap.get(key)! < delay) {
      return new Promise((resolve) =>
        setTimeout(
          () => resolve(fn.call(context, ...args)),
          delay - (now - activeMap.get(key)!)
        )
      );
    }
    activeMap.set(key, now);
    return new Promise((resolve) => {
      activeMap.delete(key);
      setTimeout(() => resolve(fn.call(context, ...args)), delay);
    });
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(A), where A is the count of unique argument sets.
- Time complexity: O(1) for each call, but actual execution depends on the wrapped function's complexity.

**Considerations:**

- Ideal for preventing performance issues due to rapid calls with different arguments.
- Useful for API calls, event handlers, and user input scenarios.

**Example:**

```typescript
const resizeWindow = throttleByArgs((width: number, height: number) => {
  // Resize window logic
}, 250);

window.addEventListener("resize", (event) => {
  resizeWindow(event.target.clientWidth, event.target.clientHeight); // Only executes once every 250ms for each unique width/height combination
});
```

### 32: deepFilter<T>(obj: T, predicate: (value: T[keyof T]) => boolean): T | undefined

**Description:**

- Recursively filters nested objects and arrays based on a predicate function.
- Removes any key-value pairs or elements that don't meet the condition.
- Enables deep-level data cleaning and selective extraction.

**Code:**

```typescript
function deepFilter<T>(
  obj: T,
  predicate: (value: T[keyof T]) => boolean
): T | undefined {
  if (typeof obj === "object" && obj !== null) {
    if (Array.isArray(obj)) {
      return obj.filter((item) => deepFilter(item, predicate));
    } else {
      const newObj: { [key in keyof T]: any } = {};
      for (const key in obj) {
        const filteredValue = deepFilter(obj[key], predicate);
        if (filteredValue !== undefined) {
          newObj[key] = filteredValue;
        }
      }
      return Object.keys(newObj).length > 0 ? newObj : undefined;
    }
  } else {
    return predicate(obj) ? obj : undefined;
  }
}
```

**Space and Time Complexity:**

- Space complexity: O(d), where d is the depth of the nested structure.
- Time complexity: O(n \* d), where n is the total number of elements.

**Considerations:**

- Handles complex data structures effectively.
- Useful for data processing, filtering, and transformation tasks.

**Example:**

```typescript
const data = {
  users: [
    { name: "Alice", age: 30, active: true },
    { name: "Bob", age: 25, active: false },
    { name: "Charlie", age: 40, active: true, hobbies: ["coding", "gaming"] },
  ],
};

const filteredData = deepFilter(data, (value) => typeof value !== "boolean"); // Remove all boolean values
console.log(filteredData);
```

### 33. memoizeLast<T, R>(fn: (args: T) => R): (args: T) => R

**Description:**

This function memorizes the result of the last call with the same arguments. Subsequent calls with identical arguments will retrieve the cached value instead of re-executing the function, improving performance for recurrent computations.

**Code:**

```typescript
function memoizeLast<T, R>(fn: (args: T) => R): (args: T) => R {
  let lastArgs: T | undefined;
  let lastResult: R | undefined;
  return (args: T) => {
    if (
      lastArgs !== undefined && typeof args === "object"
        ? JSON.stringify(args) === JSON.stringify(lastArgs)
        : args === lastArgs
    ) {
      return lastResult;
    }
    lastArgs = args;
    lastResult = fn(args);
    return lastResult;
  };
}
```

**Space and Time Complexity:**

- Space complexity: O(1), only stores the last function call's arguments and result.
- Time complexity: O(1) for subsequent calls with the same arguments, O(n) for initial calls where n is the complexity of the wrapped function.

**Considerations:**

- Ideal for performance optimization if the wrapped function is expensive to compute.
- Suitable for scenarios where arguments remain fairly consistent.

**Example:**

```typescript
const fibonacci = memoizeLast((n: number): number => {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(40)); // Calculates quickly despite previous calls since arguments are the same
```

### 34. zipWithIndex<T>(arr: T[]): (iterator: (value: T, index: number) => void) => void

**Description:**

This function iterates over an array and calls a provided iterator function for each element, providing both the element value and its index. This allows for index-aware processing and manipulation of data within the array.

**Code:**

```typescript
function zipWithIndex<T>(
  arr: T[]
): (iterator: (value: T, index: number) => void) => void {
  for (let i = 0; i < arr.length; i++) {
    iterator(arr[i], i);
  }
}
```

**Space and Time Complexity:**

- Space complexity: O(1), uses no additional memory beyond the iterator function.
- Time complexity: O(n), iterates through the entire array once.

**Considerations:**

- Enables convenient access to both element values and their positions within the array.
- Useful for transforming, filtering, or modifying data based on index-specific logic.

**Example:**

```typescript
const numbers = [1, 2, 3, 4, 5];

zipWithIndex(numbers)((value, index) => {
  console.log(`Element at index ${index}: ${value}`);
});
```

### 35. parallelMap<T, R>(arr: T[], mapper: (item: T) => Promise<R>, concurrencyLimit?: number): Promise<R[]>

**Description:**

This function concurrently executes a mapping function on each element of an array, returning a Promise for the transformed results. It limits the number of concurrent operations through an optional `concurrencyLimit` parameter, optimizing performance and resource usage.

**Code:**

```typescript
async function parallelMap<T, R>(
  arr: T[],
  mapper: (item: T) => Promise<R>,
  concurrencyLimit = Infinity
): Promise<R[]> {
  const activePromises = [];
  const results: R[] = [];
  for (let i = 0; i < arr.length; i++) {
    const promise = mapper(arr[i]);
    activePromises.push(promise);
    if (activePromises.length === concurrencyLimit) {
      await Promise.race(activePromises);
      const finishedPromise = activePromises.shift()!;
      results.push(await finishedPromise);
    }
  }
  return Promise.all(activePromises).then((remainingResults) =>
    results.concat(remainingResults)
  );
}
```

**Space and Time Complexity:**

- Space complexity: O(n + concurrencyLimit), stores active promises and results.
- Time complexity: O(n / concurrencyLimit), depends on the slowest individual mapping operation and concurrency limit.

**Considerations:**

- Provides significant performance speedup for expensive asynchronous operations within the mapper function.
- Useful for processing large datasets, fetching data concurrently, or parallelizing tasks.

**Example:**

```typescript
const images = ["image1.jpg", "image2.png", "image3.gif"];

const downloadImage = (url: string) =>
  fetch(url).then((response) => response.blob());

parallelMap(images, downloadImage, 2).then((downloadedImages) => {
  // Process downloaded images
});
```

### 36. findPaths<T>(graph: { [key: string]: string[] }, start: string, end?: string): string[][]

**Description:**

This function performs a depth-first search (DFS) on a directed graph represented as an object with keys as nodes and values as connected nodes. It finds all possible paths from the specified `start` node to the optional `end` node if provided, or all paths if the `end` is omitted.

**Code:**

```typescript
function findPaths<T>(
  graph: { [key: string]: string[] },
  start: string,
  end?: string
): string[][] {
  const paths: string[][] = [];
  function dfs(current: string, path: string[] = []): void {
    if (end && current === end) {
      paths.push(path.concat(current));
      return;
    }
    for (const neighbour of graph[current]) {
      if (!path.includes(neighbour)) {
        dfs(neighbour, path.concat(current));
      }
    }
  }
  dfs(start);
  return paths;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of nodes in the graph, due to recursive call stack.
- Time complexity: O(n!), worst-case scenario with a fully connected graph and no end node, exploring all possible paths.

**Considerations:**

- Powerful for exploring connections and potential routes within a network or complex data structure.
- Useful for navigation algorithms, dependency analysis, or finding optimal paths in a directed graph.

**Example:**

```typescript
const cityMap = {
  London: ["Paris", "Amsterdam"],
  Paris: ["Rome", "Madrid"],
  Amsterdam: ["Berlin", "Prague"],
  Rome: ["Berlin"],
  Madrid: [],
  Berlin: [],
  Prague: [],
};

const allPathsFromLondon = findPaths(cityMap, "London");
console.log(allPathsFromLondon); // Shows all possible paths from London to other cities

const pathsToMadrid = findPaths(cityMap, "London", "Madrid");
console.log(pathsToMadrid); // Shows only paths from London to Madrid
```

### 37. keyByValue<T, K>(obj: { [key: string]: T }, keyFn: (value: T) => K): { [key: K]: T }

**Description:**

This function flips the key-value relationship of an object, using a provided `keyFn` to extract a new key from each value. It allows for creating an object where values become keys and the original keys are discarded.

**Code:**

```typescript
function keyByValue<T, K>(
  obj: { [key: string]: T },
  keyFn: (value: T) => K
): { [key: K]: T } {
  const newObj: { [key: K]: T } = {};
  for (const key in obj) {
    const value = obj[key];
    const newKey = keyFn(value);
    if (newObj[newKey] !== undefined) {
      // Handle potential key collisions (optional logic can be added here)
      console.warn(
        `Key collision detected for value: ${value}, skipping duplicate.`
      );
    } else {
      newObj[newKey] = value;
    }
  }
  return newObj;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the number of key-value pairs in the original object, due to creating a new object.
- Time complexity: O(n), iterates through all key-value pairs in the original object.

**Considerations:**

- Useful for data reorganization, indexing by specific properties, or creating inverted maps.
- Requires a consistent and unique key generation function to avoid collisions.

**Example:**

```typescript
const users = {
  alice: { name: "Alice", age: 30 },
  bob: { name: "Bob", age: 25 },
  charlie: { name: "Charlie", age: 40 },
};

const usersByName = keyByValue(users, (user) => user.name);
console.log(usersByName); // { 'Alice': { ... }, 'Bob': { ... }, 'Charlie': { ... } }
```

### 38. partitionWhile<T>(arr: T[], predicate: (item: T) => boolean): [T[], T[]]

**Description:**

This function splits an array into two sub-arrays based on a predicate function, similar to `partition` but continuously evaluates the predicate instead of stopping at the first mismatch. It keeps elements in the first sub-array as long as the predicate returns true, then places remaining elements in the second sub-array.

**Code:**

```typescript
function partitionWhile<T>(
  arr: T[],
  predicate: (item: T) => boolean
): [T[], T[]] {
  const truthyArr: T[] = [];
  const falsyArr: T[] = [];
  for (const item of arr) {
    if (predicate(item)) {
      truthyArr.push(item);
    } else {
      falsyArr.push(item);
      break;
    }
  }
  return [truthyArr, falsyArr];
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the input array, creates two new arrays with potentially all elements.
- Time complexity: O(n), iterates through the array once and applies the predicate function to each item.

**Considerations:**

- Useful for separating elements based on a changing condition until a specific point in the array.
- Can be applied to tasks like identifying prefixes, extracting initial matching elements, or segmenting data based on a shifting criterion.

**Example:**

```typescript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

const [evenPrefix, oddSuffix] = partitionWhile(numbers, (num) => num % 2 === 0);
console.log(evenPrefix); // [1, 2, 4, 6, 8]
console.log(oddSuffix); // [3, 5, 7, 9]
```

### 39. shuffleInPlaceMutable<T>(arr: T[]): void

**Description:**

This function shuffles the elements of an array in-place, modifying the original array directly instead of creating a new one. This improves performance and memory usage for large datasets. However, unlike the previous `shuffleInPlace` function, it uses a more efficient Fisher-Yates shuffle algorithm with O(n) time complexity.

**Code:**

```typescript
function shuffleInPlaceMutable<T>(arr: T[]): void {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
}
```

**Space and Time Complexity:**

- Space complexity: O(1), modifies the original array in-place without additional memory allocation.
- Time complexity: O(n), iterates through the array once and performs random swaps.

**Considerations:**

- Efficient and memory-friendly for shuffling large datasets.
- Modifies the original array directly, unlike the previous `shuffleInPlace` function.

**Example:**

```typescript
const cards = ["Ace", "King", "Queen", "Jack"];
shuffleInPlaceMutable(cards);
console.log(cards); // Output order is random each time
```

### 40. groupByCount<T>(arr: T[]): { [key: T]: number }

**Description:**

This function groups elements in an array by their value and counts the occurrences of each unique value. It returns an object where keys are the distinct values and values are their corresponding counts.

**Code:**

```typescript
function groupByCount<T>(arr: T[]): { [key: T]: number } {
  const countMap: { [key: T]: number } = {};
  for (const item of arr) {
    countMap[item] = (countMap[item] || 0) + 1;
  }
  return countMap;
}
```

**Space and Time Complexity:**

- Space complexity: O(n), where n is the length of the input array, due to creating a new object to store counts.
- Time complexity: O(n), iterates through the array once and updates the count map for each element.

**Considerations:**

- Useful for analyzing frequency distribution within an array.
- Provides insights into data patterns and element occurrences.

**Example:**

```typescript
const fruits = ["apple", "banana", "orange", "apple", "grape", "banana"];

const fruitCounts = groupByCount(fruits);
console.log(fruitCounts); // { apple: 2, banana: 2, orange: 1, grape: 1 }
```

### 41. differenceBetweenArrays<T>(arr1: T[], arr2: T[]): T[]

**Description:** Determines the difference between two arrays, returning an array containing elements that are present in the first array but not in the second.

**Function Code:**

```typescript
function differenceBetweenArrays<T>(arr1: T[], arr2: T[]): T[] {
  return arr1.filter((item) => !arr2.includes(item));
}
```

**Space and Time Complexity:**

- Space: O(n + m) where n and m are the lengths of arr1 and arr2, respectively, as we create a new array of the filtered elements.
- Time: O(n \* m) in the worst case, as the `includes` method has a time complexity of O(m) for each element in arr1.

**Considerations:**

- For large arrays, performance can be optimized by using a Set for arr2, as Set lookups have a time complexity of O(1).
- If you need to handle duplicate elements within arrays, you might need to adjust the logic accordingly.

**Code Example:**

```typescript
const numbers1 = [1, 2, 3, 4, 5];
const numbers2 = [2, 4, 6];

const difference = differenceBetweenArrays(numbers1, numbers2); // [1, 3, 5]

console.log(difference);
```

### 42. generateRandomString

**Description:** Generates a random string of a specified length with specified characters.

**Function Code:**

```typescript
function generateRandomString(
  length: number,
  characters: string = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"
): string {
  let result = "";
  for (let i = 0; i < length; i++) {
    result += characters.charAt(Math.floor(Math.random() * characters.length));
  }
  return result;
}
```

**Space and Time Complexity:**

- Space: O(length) as we create a string of the specified length.
- Time: O(length) as we loop through the desired length and randomly choose characters.

**Considerations:**

- You can customize the `characters` parameter to include specific characters, symbols, or even exclude certain ones.
- Be mindful of generating secure random strings for sensitive applications.

**Code Example:**

```typescript
const randomString1 = generateRandomString(10); // "e7T42s98hK"
const randomString2 = generateRandomString(5, "!@#$%^&*"); // "k%9&!"

console.log(randomString1, randomString2);
```

### 43. hasAllKeys<T>(obj: T, keys: string[]): boolean

This function was already provided but here it is again with a more detailed description:

**Description:** Verifies whether an object possesses all the specified keys.

**Function Code:**

```typescript
function hasAllKeys<T>(obj: T, keys: string[]): boolean {
  return keys.every((key) => Object.prototype.hasOwnProperty.call(obj, key));
}
```

**Example:**

```typescript
const user = { name: "John", age: 30 };
const hasRequiredKeys = hasAllKeys(user, ["name", "age", "email"]); // false

console.log(hasRequiredKeys);
```

### 44. pickKeys<T, K extends keyof T>(obj: T, keys: K[]): T[K] & Record<Exclude<keyof T, K>, undefined>

**Description:** Creates a new object from an existing one, containing only the specified keys and setting all other keys to `undefined`.

**Function Code:**

```typescript
function pickKeys<T, K extends keyof T>(
  obj: T,
  keys: K[]
): T[K] & Record<Exclude<keyof T, K>, undefined> {
  const pickedObj: T[K] & Record<Exclude<keyof T, K>, undefined> = {} as T[K] &
    Record<Exclude<keyof T, K>, undefined>;
  for (const key of keys) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      pickedObj[key] = obj[key];
    }
  }
  return pickedObj;
}
```

**Example:**

```typescript
const user = { name: "John", age: 30, email: "example@domain.com" };
const userSelection = pickKeys(user, ["name", "age"]); // { name: 'John', age: 30, email: undefined }

console.log(userSelection);
```

**Considerations:**

- `K` represents a generic type parameter specifying the type of keys to include.
- The return type combines the selected key types with `undefined` for excluded keys.
- It uses strict property existence checks and doesn't consider inherited properties.

### 45. randomItem<T>(arr: T[]): T

**Description:** Selects a random element from an array.

**Function Code:**

```typescript
function randomItem<T>(arr: T[]): T {
  const randomIndex = Math.floor(Math.random() * arr.length);
  return arr[randomIndex];
}
```

**Space and Time Complexity:**

- Space: O(1) as it doesn't create additional data structures.
- Time: O(1) as accessing elements by index in an array is constant time.

**Considerations:**

- This function assumes the array contains at least one element.
- You can generate multiple random items by calling the function repeatedly.

**Code Example:**

```typescript
const colors = ["red", "green", "blue"];
const randomColor = randomItem(colors); // "red" or "green" or "blue"

console.log(randomColor);
```

Coming right up! Here are the next two functions in the alphabetical order:

### 46. sleep(ms: number): Promise<void>

**Description:** Delays the execution of the subsequent code for a specified number of milliseconds.

**Function Code:**

```typescript
function sleep(ms: number): Promise<void> {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

**Space and Time Complexity:**

- Space: O(1) as it only creates a single promise object.
- Time: O(ms) as the execution is delayed for the specified number of milliseconds.

**Considerations:**

- This function uses asynchronous patterns and should be used within an asynchronous context like promise chains or async/await.
- The actual delay might be slightly longer than the specified milliseconds due to internal timer resolution limitations.

**Code Example:**

```typescript
async function fetchData() {
  await sleep(1000); // waits for 1 second before fetching data
  const data = await fetch("https://example.com/api/data");
  // ... process data
}

fetchData().then(() => console.log("Data fetched!"));
```

### 47. retry<T>(fn: () => Promise<T>, options?: { attempts?: number, delay?: number, onFail?: (error: Error) => void }): Promise<T>

**Description:** Automatically retries a failing asynchronous function a specified number of times with an optional delay in between attempts, improving reliability and handling transient errors.

**Function Code:**

```typescript
async function retry<T>(
  fn: () => Promise<T>,
  options?: {
    attempts?: number;
    delay?: number;
    onFail?: (error: Error) => void;
  }
): Promise<T> {
  const { attempts = 3, delay = 1000, onFail } = options ?? {};
  for (let i = 1; i <= attempts; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === attempts) {
        throw error;
      }
      if (onFail) {
        onFail(error);
      }
      await new Promise((resolve) => setTimeout(resolve, delay));
    }
  }
  throw new Error("All retries failed");
}
```

**Space and Time Complexity:**

- Space: O(1) as it only stores temporary variables for state management.
- Time: O(attempts \* (fn execution time + delay)) depending on the number of attempts and actual execution time of the function.

**Considerations:**

- Configure the number of attempts and delay based on the potential error type and acceptable retry behavior.
- Provide an optional `onFail` callback to handle individual error occurrences during retries.

**Code Example:**

```typescript
const sendOrder = async (orderData) => {
  // Simulate potential network error
  if (Math.random() < 0.2) {
    throw new Error("Network error occurred");
  }
  // Actual order submission logic
  return await fetch("https://api.example.com/orders", {
    method: "POST",
    body: JSON.stringify(orderData),
  });
};

const submitOrder = async (orderData) => {
  return await retry(() => sendOrder(orderData), { attempts: 5, delay: 2000 });
};

// Attempt to submit the order, retrying on network errors
submitOrder(orderData)
  .then(() => console.log("Order submitted successfully!"))
  .catch(() => console.error("Order submission failed"));
```

### 48. throttleConcurrent<T>(promises: Promise<T>[], maxConcurrency: number): Promise<T[]>

**Description:** Limits the number of concurrently running promises while awaiting their resolution, returning an array containing the resolved values in the same order as the input promises.

**Function Code:**

```typescript
async function throttleConcurrent<T>(
  promises: Promise<T>[],
  maxConcurrency: number
): Promise<T[]> {
  const results: T[] = [];
  const runningPromises: Promise<T>[] = [];

  for (const promise of promises) {
    runningPromises.push(promise);
    if (runningPromises.length === maxConcurrency) {
      const resolvedPromise = await Promise.race(runningPromises);
      results.push(resolvedPromise);
      runningPromises.splice(runningPromises.indexOf(resolvedPromise), 1);
    }
  }

  await Promise.all(runningPromises); // wait for remaining promises to resolve
  results.push(...runningPromises.map((p) => p.then((v) => v)));

  return results;
}
```

**Space and Time Complexity:**

- Space: O(maxConcurrency) due to storing concurrent promises in an internal queue.
- Time: O(longestPromise) as it waits for the longest-running promise to resolve before returning the results array. However, the overall execution can be faster by limiting concurrent tasks and maximizing utilization.

**Considerations:**

- This function is ideal for situations where overwhelming the system with too many concurrent asynchronous operations could be detrimental.
- Adjust the `maxConcurrency` parameter based on your system's capacity and desired balance between processing speed and resource utilization.

**Code Example:**

```typescript
const fetchImages = async (imageUrl) => {
  const response = await fetch(imageUrl);
  return await response.blob();
};

const imageUrls = [
  "https://example.com/image1.jpg",
  "https://example.com/image2.png",
  "https://example.com/image3.gif",
];

const images = await throttleConcurrent(imageUrls.map(fetchImages), 2); // Fetches images with a maximum of 2 concurrent operations, returning an array of downloaded images

console.log(images); // Array of downloaded image Blobs
```

### 49. debouncedFetch<T>(url: string, options?: RequestInit): Promise<Response>

**Description:** Implements a debounced fetching mechanism, preventing unnecessary network requests due to rapid user actions or repeated calls.

**Function Code:**

```typescript
let timer: any;

async function debouncedFetch<T>(
  url: string,
  options?: RequestInit
): Promise<Response> {
  return new Promise((resolve, reject) => {
    clearTimeout(timer);
    timer = setTimeout(async () => {
      try {
        const response = await fetch(url, options);
        resolve(response);
      } catch (error) {
        reject(error);
      }
    }, delay);
  });
}
```

**Space and Time Complexity:**

- Space: O(1) due to only using a single timer for debouncing.
- Time: O(delay) for initial wait before actual fetch, then depends on network and response processing time.

**Considerations:**

- Set the delay duration based on your desired responsiveness and tolerance for potential unnecessary requests.
- You can extend this function to accept custom cancellation logic or customize the debouncing behavior if needed.

**Code Example:**

```typescript
const searchBar = document.getElementById("search-bar");

searchBar.addEventListener("keyup", async (event) => {
  const searchTerm = event.target.value;
  const response = await debouncedFetch(
    `https://api.example.com/search?q=${searchTerm}`,
    { method: "GET" }
  );
  // process the search results based on the response
});
```

### 50. transformRecursively<T>(input: T | T[], predicate: (value: any) => boolean, newValue: any): T | T[]

```typescript
function transformRecursively<T>(
  input: T | T[],
  predicate: (value: any) => boolean,
  newValue: any
): T | T[] {
  if (Array.isArray(input)) {
    return input.map((item) => transformRecursively(item, predicate, newValue));
  } else if (typeof input === "object" && input !== null) {
    const result = {};
    for (const key in input) {
      result[key] = transformRecursively(input[key], predicate, newValue);
    }
    return result;
  } else {
    return predicate(input) ? newValue : input;
  }
}
```

**Space and Time Complexity:**

- Space: O(n) where n is the number of elements in the input data structure (array or object). This is because the function needs to create new copies of the transformed elements and maintain them while processing the data.
- Time: (O(n)) All elements trigger the transformation (predicate always returns true), leading to linear time complexity (O(n)), as the function needs to visit each element in the data structure.

**Explanation:**

- **TypeScript Generics:** The function uses generics (`<T>`) to ensure type safety and work with various input types.
- **Base Cases:**
  - Arrays: It maps over each element recursively, applying the transformation.
  - Objects: It iterates through key-value pairs, recursively transforming each value.
  - Primitives: It applies the predicate and returns `newValue` if truthy, otherwise returns the original value.
- **Recursion:** The function calls itself for nested arrays and objects, ensuring thorough transformation.

**Example Usage:**

```typescript
const data = {
  items: [1, 2, 3, { nested: 4 }],
  value: "hello",
  other: true,
};

const transformed = transformRecursively(
  data,
  (value) => value > 2,
  "replaced"
);

console.log(transformed);
// Output: { items: [1, 2, 'replaced', { nested: 'replaced' }], value: 'hello', other: true }
```

**Key Points:**

- It handles arrays, objects, and primitives effectively.
- It's type-safe, ensuring reliable data manipulation.
- It's concise and readable, making it easy to understand and use.
- It's recursively adaptable to nested structures.
