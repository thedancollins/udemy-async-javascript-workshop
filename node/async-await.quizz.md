# Question 1

Convert the promise version of the multi-file loader over to using async/await

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

const files = ["./files/demofile.txt", "./files/demofile.other.txt"];

//this is synchronous and not as efficient as the Promise.all implementation below
(async () => {
  for (let file of files) {
    let value = await readFile(file, "utf8");
    console.log(value);
  }
})();
```

/_
(async () => {
let promises = files.map(name => readFile(name, "utf8"));
let values = await Promise.all(promises);
console.log(values);
})();_/

# Question 2

Again convert the promise version of the multi-file loader over to using async/await but using a custom async iterator with the following syntax

node --harmony-async-iteration <file.js>

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

const fileIterator = files => ({
  [Symbol.asyncIterator]: () => ({
    x: 0,
    next() {
      if (this.x >= files.length) {
        return Promise.resolve({
          done: true
        });
      }

      let file = files[this.x++];
      return readFile(file, "utf8").then(data => ({
        done: false,
        value: data
      }));
      // alternative syntax: return readFile(file, "utf8").then(data => { return { done: false, value: data}});
      console.log(res);
      let y = this.x++;

      return Promise.resolve({
        done: false,
        value: y
      });
    }
  })
});

(async () => {
  for await (let x of fileIterator([
    "./files/demofile.txt",
    "./files/demofile.other.txt"
  ])) {
    console.log(x);
  }
})();
```
