# Quizz 1

Using you're knowledge of the event loop, create a program which prints out the below. If the log line mentions a `setInterval` it must be printed inside a `setInterval` etc..

start
end
setInterval 1
promise 1
promise 2

```js
console.log("start");
const interval = setInterval(() => {
  console.log("setInterval 1");
  Promise.resolve()
    .then(() => {
      debugger;
      console.log("promise 1");
    })
    .then(() => {
      debugger;
      console.log("promise 2");
    }, 0);
  clearInterval(interval);
});
console.log("end");
```

# Quizz 2

Extend the previous example to print out the following log lines, use `process.nextTick` and `setImmediate`

start
end
setInterval 1
promise 1
promise 2
processNextTick 1
setImmediate 1
promise 3
promise 4

```js
console.log("start");
const interval = setInterval(() => {
  console.log("setInterval 1");
  Promise.resolve()
    .then(() => {
      debugger;
      console.log("promise 1");
    })
    .then(() => {
      debugger;
      console.log("promise 2");
      setImmediate(() => {
        console.log("setImmediate 1");
        Promise.resolve()
          .then(() => {
            debugger;
            console.log("promise 3");
          })
          .then(() => {
            debugger;
            console.log("promise 4");
            clearInterval(interval);
          });
      });
      process.nextTick(() => {
        console.log("processNextTick 1");
      });
    }, 0);

  /* This works but it is cheating - need to understand that process.nextTick is going to run BEFORE the setImmediate
  process.nextTick(() => {
    setImmediate(() => {
      console.log("processNextTick 1");
      console.log("setImmediate 1");
      Promise.resolve()
        .then(() => {
          debugger;
          console.log("promise 3");
        })
        .then(() => {
          debugger;
          console.log("promise 4");
        });
    });
  });
  
  clearInterval(interval);*/
});
console.log("end");
```
