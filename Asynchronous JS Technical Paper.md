# Technical Paper on JavaScript Asynchronous Concepts & Promises

---

## 1. How JavaScript Executes Code ?

- JavaScript is a single-threaded and synchronous language by default.
- It uses a Call Stack to execute code line by line.
- When a function runs, it is pushed onto the call stack; when it finishes, it is popped off.

- Simple Example

    ``` javascript
    function a(){ console.log("A"); }
    function b(){ console.log("B"); }
    a();
    b();
    
    Output:
    A
    B
    ```

---

## 2. What is diff between Sync & Async ?

- | Feature | Synchronous | Asynchronous |
  | ------- | ----------- | ------------ |
  | Execution |	Line by line | Does not block execution |
  | Waiting | Blocks next code until completed | Allows parallel waiting |
  | Example | loops, normal functions | setTimeout, fetch, promises, async/await |

- Simple Example

    ``` javascript
    console.log("Start");
    setTimeout(() => console.log("Async Task"), 2000);
    console.log("End");
        
    Output:
    Start
    End
    Async Task
    ```

---

## 3. What are the ways to make the code Async?

- setTimeout()
- setInterval()
- Web Browser APIs (fetch, DOM events, geolocation)
- Promises
- async/await
- Web Workers

---

## 4. What are Web Browser APIs?

- They are APIs provided by browsers to perform complex tasks without blocking JS.
- They run outside JavaScript engine.

- Examples

    - fetch()
    - DOM Event Listeners
    - LocalStorage
    - Geolocation
    - Timers (setTimeout, setInterval)

---

## 5. What is event loop?

- The Event Loop manages asynchronous callbacks.
- It checks the Call Stack, and if empty, pushes tasks from the Callback Queue / Microtask queue into it.

- Diagram

    ``` Diagram
    Call Stack ⬅ Event Loop ⬅ Callback Queue ⬅ Web APIs
    ```

---

## 6. What is a callback hell ?

- A situation where nested callbacks create unreadable code.

- Simple Example

    ``` javascript
    setTimeout(() => {
        setTimeout(() => {
            setTimeout(() => {
                console.log("Done");
            }, 1000);
        }, 1000);
    }, 1000);
    ```

- Hard to maintain and debug.

---

## 7. What is inversion of control in callbacks?

- When you pass a callback to another function, you lose control of when/how it runs.

- Simple Example

    ``` javascript
    function register(cb){
        cb(); 
    }
    ```

---

# Promises

---

## 8. What is a Promise ?

- A Promise is an object representing the eventual completion or failure of an asynchronous task.

---

## 9. How to create a new promise ?

- Code Example

    ``` javascript
    const p = new Promise((resolve, reject) => {
        let success = true;
        success ? resolve("Done") : reject("Failed");
    });

---

## 10. What are different states of a Promise `pending`, `fulfilled`, `rejected` ?

- | State |	Description |
  | Pending | waiting |
  | Fulfilled |	resolve() executed |
  | Rejected | reject() executed |

---

## 11. How to consume an existing promise ?

- Code Example

    ``` javascript
    p.then(result => console.log(result))
     .catch(error => console.log(error));
    ```

---

# Promise Chaining

---

## 12. How to chain promises using .then ?

- Code Example

    ``` javascript
    p.then(data => {
        console.log(data);
        return "Step 2";
    }).then(next => console.log(next));
    ```

---

## 13. How to handle errors in a promise chain using .catch ?

- Code Example

    ``` javascript
    p.then(res => JSON.parse(res))
     .catch(err => console.log("Error:", err));
    ```

---

## 14. Finally block in a Promise Chain

- Code Example

    ``` javascript
    p.finally(() => console.log("Always runs"));
    ```

---

## 15. What happens when an Error gets thrown inside .then when there is a `.catch` ?

- If an error is thrown inside `.then`, it immediately skips the remaining `.then` blocks and goes to the nearest `.catch` handler.

- Code Example

    ``` javascript
    p.then(() => { throw new Error("Failed"); })
     .catch(err => console.log(err.message));
    ```

---

## 16. What happens when an Error gets thrown inside `.then` when there is no `.catch` ?

- If an error is thrown inside `.then` and there is no `.catch`, the promise becomes rejected and results in an unhandled promise rejection.

- Code Example

    ``` javascript
    p.then(() => { throw new Error("Error"); });
    ```

-  Result: unhandled promise rejection

---

## 17. Why must `.catch` be placed towards the end of the promise chain?

- Because placing `.catch` at the end ensures it can handle any errors that occur in any preceding `.then` block throughout the promise chain.

---

## 18. How to consume multiple promises by chaining?

- To consume multiple promises by chaining, you return a new promise inside `.then()` so the next `.then()` waits for it to resolve.

- Code Example

``` javascript
firstPromise()
  .then(result1 => {
    console.log(result1);
    return secondPromise();   
  })
  .then(result2 => {
    console.log(result2);
    return thirdPromise();
  })
  .then(result3 => {
    console.log(result3);
  })
  .catch(error => {
    console.error("Error:", error);
  });

```

---

## 19. How to consume multiple promises by Promise.all?

- To consume multiple promises with Promise.all, you pass an array of promises to it.
- Promise.all waits for all promises to resolve, then returns an array of results in the same order.

- Code Example

    ``` javascript
    Promise.all([promise1(), promise2(), promise3()])
           .then(results => {
                    console.log(results); 
    })
           .catch(error => {
                    console.error("Error:", error);
    });
    ```

---

## 20. How to do error handling when using promises?

- Error handling in promises is mainly done using .catch(), and by returning / throwing errors properly in the chain.

- ### Using .catch() at the end

    ``` javascript
    doSomething()
        .then(result => {
            console.log(result);
        })
        .catch(error => {
            console.error("Error occurred:", error);
        });
    ```

- Any error thrown in any .then() jumps to .catch().

- ### Chaining with catch

    ``` javascript
    doSomething()
        .then(result => {
            if (!result) throw new Error("Invalid result");
            return doNext(result);
        })
        .then(r => console.log(r))
        .catch(err => console.log("Caught:", err));
    ```

- ### Using .catch() after specific .then()

- You can isolate error handling to part of the chain:

    ``` javascript
    doStep1()
        .then(res => doStep2(res))
        .catch(err => console.log("Error in step 1 or 2"))
        .then(() => doStep3())
        .catch(err => console.log("Error in step 3"));
    ```

- ### Using .finally()

- Used for cleanup (runs whether resolved or rejected):

    ``` javascript
    doTask()
        .then(res => console.log(res))
        .catch(err => console.log(err))
        .finally(() => console.log("Done!"));
    ```

- ### Handling errors in Promise.all

    ``` javascript
    Promise.all([p1(), p2(), p3()])
           .then(results => console.log(results))
           .catch(err => console.log("One promise failed:", err));
    ```

- If any promise rejects → catch is triggered.

- ### Using async/await try–catch

    ``` javascript
    async function run() {
        try {
            const r1 = await p1();
            const r2 = await p2();
            console.log(r1, r2);
        } catch (err) {
            console.error("Error:", err);
        }
    }
    run();
    ```

---

## 21. Why is error handling the most important part of using a promise?

- Key Reasons:

    - Promises run asynchronously
        Errors may occur when backend responses fail, network issues happen, or invalid data is returned. Without .catch(), you won’t know something went wrong.

    - Prevents breaking the entire promise chain
        If an error occurs in any .then(), the rest of the chain stops and .catch() handles it cleanly instead of crashing the program.

    - Helps maintain predictable flow
        Proper error handling ensures you always know what happens when something fails, so you can retry, log errors, or show proper messages.

    - Avoids uncaught promise rejections
        Uncaught errors can cause warnings or crashes like:
        ``` comment
        UnhandledPromiseRejectionWarning
        ```

    - Improves user experience
        Instead of the application freezing, you can:
            - Show a friendly message
            - Retry an API call
            - Perform fallback operations

    - Example

        ``` javascript
        fetchData()
            .then(res => process(res))
            .catch(err => {
                console.error("Something went wrong:", err);
                showErrorUI();
        });
        ```

---

## 22. How to promisify an asynchronous callbacks based function - eg `. setTimeout`, `fs.readFile` ?

- To promisify an asynchronous callback-based function means to wrap it inside a Promise so you can use .then(), .catch(), or async/await.

- Promisify setTimeout Example
    ``` javascript
    function wait(ms) {
        return new Promise(resolve => {
            setTimeout(() => {
                resolve(`Waited for ${ms} ms`);
            }, ms);
        });
    }

    wait(1000).then(msg => console.log(msg));
    ```

- Promisify fs.readFile (Node.js)

    ``` javascript
    const fs = require("fs");
    function readFileAsync(path) {
        return new Promise((resolve, reject) => {
            fs.readFile(path, "utf8", (err, data) => {
                if (err) reject(err);    
                else resolve(data);
            });
        });
    }

    readFileAsync("test.txt")
        .then(content => console.log(content))
        .catch(err => console.log("Error:", err));
    ```

- Using util.promisify (built-in Node utility)

    ``` javascript
    const fs = require("fs");
    const util = require("util");

    const readFilePromise = util.promisify(fs.readFile);

    readFilePromise("test.txt", "utf8")
        .then(data => console.log(data))
        .catch(err => console.error(err));
    ```

---

## 23. How to use the following promise based functions ?

### 23.1. Promise.all()

- Runs promises in parallel and waits for all to complete.
- Fails if any promise fails.

    ``` javascript
    Promise.all([p1, p2, p3])
           .then(console.log)
           .catch(console.error);
    ```

---

### 23.2. Promise.allSettled()

- Code Example

    ``` javascript
    Promise.allSettled([p1, p2])
           .then(console.log);
    ```

---

### 23.3. Promise.any()

- Resolves when the first resolved promise arrives.

    ``` javascript
    Promise.any([p1, p2]).then(console.log);
    ```

---

### 23.4. Promise.race()

- Returns result of the first completed promise (success or failure).

    ``` javascript
    Promise.race([p1, p2]).then(console.log);
    ```

---

### 23.5. Promisifying Callback Functions

- Code Example

    ``` javascript
    function wait(ms){
        return new Promise(resolve => setTimeout(resolve, ms));
    }
    wait(2000).then(() => console.log("Done"));
    ```

---

### 23.6. Promise.resolve and Promise.reject

- Code Example

    ``` javascript
    Promise.resolve("Done");
    Promise.reject("Error");
    ```

---

## References

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
- https://javascript.info/promise-basics

---