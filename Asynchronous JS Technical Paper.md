# Technical Paper on JavaScript Asynchronous Concepts & Promises

---

## 1. How JavaScript Executes Code

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

## 2. Sync vs Async

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

## 3. Ways to Make Code Asynchronous

- setTimeout()
- setInterval()
- Web Browser APIs (fetch, DOM events, geolocation)
- Promises
- async/await
- Web Workers

---

## 4. What Are Web Browser APIs?

- They are APIs provided by browsers to perform complex tasks without blocking JS.
- They run outside JavaScript engine.

- Examples

    - fetch()
    - DOM Event Listeners
    - LocalStorage
    - Geolocation
    - Timers (setTimeout, setInterval)

---

## 5. What Is Event Loop?

- The Event Loop manages asynchronous callbacks.
- It checks the Call Stack, and if empty, pushes tasks from the Callback Queue / Microtask queue into it.

- Diagram

    ``` Diagram
        Call Stack ⬅ Event Loop ⬅ Callback Queue ⬅ Web APIs
    ```

---

## 6. What Is Callback Hell?

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

## 7. What Is Inversion of Control?

- When you pass a callback to another function, you lose control of when/how it runs.

- Simple Example

    ``` javascript
        function register(cb){
            cb(); // caller decides when callback runs
        }
    ```

---

## 8. What Is a Promise?

- A Promise is an object representing the eventual completion or failure of an asynchronous task.

---

## 9. How to Create a Promise ?

- Code Example

    ``` javascript
        const p = new Promise((resolve, reject) => {
            let success = true;
            success ? resolve("Done") : reject("Failed");
        });

---

## 10. States of a Promise
- | State |	Description |
  | Pending | waiting |
  | Fulfilled |	resolve() executed |
  | Rejected | reject() executed |

---

## 11. How to Consume an Existing Promise ?

- Code Example

    ``` javascript
        p.then(result => console.log(result))
         .catch(error => console.log(error));
    ```

---

## 12. How to Chain Promises ?

-    ``` javascript
         p.then(data => {
             console.log(data);
             return "Step 2";
         }).then(next => console.log(next));
     ```

---

## 13. Error Handling Using .catch()

-    ``` javascript
         p.then(res => JSON.parse(res))
          .catch(err => console.log("Error:", err));
     ```

---

## 14. Finally in Promise Chain

-   ``` javascript
        p.finally(() => console.log("Always runs"));
    ```

---

## 15. Error Inside .then() With .catch()

-   ``` javascript
        p.then(() => { throw new Error("Failed"); })
         .catch(err => console.log(err.message));
    ```

---

## 16. Error Inside .then() Without .catch()

-   ``` javascript
        p.then(() => { throw new Error("Error"); });
    ```

-  Result: unhandled promise rejection

---

## 17. Why .catch() Must Be at End ?

- It acts as a global error handler for the entire chain.

---

## 18. Handling Multiple Promises by Chaining

-   ``` javascript
        p1.then(() => p2())
          .then(() => p3())
          .catch(console.error);
    ```

---

## 19. Promise.all()

- Runs promises in parallel and waits for all to complete.
- Fails if any promise fails.

    ``` javascript
        Promise.all([p1, p2, p3])
               .then(console.log)
               .catch(console.error);
    ```

---

## 20. Promise.allSettled()

-   ``` javascript
        Promise.allSettled([p1, p2])
               .then(console.log);
    ```

---

## 21. Promise.any()

- Resolves when the first resolved promise arrives.

    ``` javascript
        Promise.any([p1, p2]).then(console.log);
    ```

---

## 22. Promise.race()

- Returns result of the first completed promise (success or failure).

    ``` javascript
        Promise.race([p1, p2]).then(console.log);
    ```

---

## 23. Promisifying Callback Functions

-    ``` javascript
        function wait(ms){
            return new Promise(resolve => setTimeout(resolve, ms));
        }
        wait(2000).then(() => console.log("Done"));
     ```

---

## 24. Promise.resolve and Promise.reject

-   ``` javascript
        Promise.resolve("Done");
        Promise.reject("Error");
    ```

---

## 25. Why Error Handling Is Important ?

- Prevents breaking app flow
- Improves debugging
- Protects user experience

---

## References

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
- https://javascript.info/promise-basics

---