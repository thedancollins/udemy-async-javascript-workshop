# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");
const util = require("util");
const readFile = util.promisify(fs.readFile);

/*function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err)
      resolve(data);
    });
  })
}*/
readFile("./files/demofile.txt", "utf-8").then(
  val => console.log("File read: ", val),
  err => console.log("Failed to read file:", err)
);
```

# Question 2

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (error, result) => {
      if (error) reject(error);
      resolve(data);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("./files/demofile.txt", "utf-8").then(
  data => {
    zlibPromise(data).then(
      res => console.log(res),
      err => console.error("Failed", err)
    );
  },
  err => {
    console.error("Failed2:", err);
  }
);
```

# Question 3

Convert the previous code so that it now chains the promise as well.

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (error, result) => {
      if (error) reject(error);
      resolve(data);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("./files/demofile.txt", "utf-8")
  .then(
    data => {
      return zlibPromise(data);
    },
    err => {
      console.error("Failed2:", err);
    }
  )
  .then(
    res => {
      console.log(res);
    },
    err => {
      console.error("Failed", err);
    }
  );
```

# Question 4

Convert the previous code so that it now handles errors using the catch handler

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (error, result) => {
      if (error) reject(error);
      resolve(data);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("./files/demofile2.txt", "utf-8")
  .then(data => {
    return zlibPromise(data);
  })
  .then(res => {
    console.log(res);
  })
  .catch(err => {
    console.error("Failed: ", err);
  });
```

# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep, "Big File"));
}
function readFileTimeoutFake(sleep) {
  return new Promise((_, reject) => setTimeout(reject, sleep, "Timeout"));
}

Promise.race([readFileFake(5000), readFileTimeoutFake(1000)])
  .then(value => {
    console.log("Big File Donwloaded", value);
  })
  .catch(err => {
    console.log("Big File was not Downloaded due to: ", err);
  });
```

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log("Publishing");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function safePublish() {
  return publish().then(res => { if (res.status === 403 ) {  return authenticate();
  }
  return res;});
}

function timeout(sleep) {
  return new Promise((_resolve_, reject) =>
    setTimeout(reject, sleep, "timeout")
  );
}

Promise.race([safePublish(), timeout(5000)])
  .then(_value_ => {
    console.log("Published");
  })
  .catch(err => {
    if (err === "timeout") {
      console.error("Timeout Failure:", err);
    }
    else {
      console.error("Unknown Failure::", err);
    }

  });
```
