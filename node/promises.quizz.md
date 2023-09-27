# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data);
      }
    });
  });
}
readFile("./files/de2mofile.txt", "utf-8").then(
  (data) => console.log(data),
  (err) => console.error(err)
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
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
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
  (val) => {
    zlibPromise(val).then(
      (val) => console.log(val),
      (err) => console.error(err)
    );
  },
  (err) => console.error(err)
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
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
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
    (val) => {
      return zlibPromise(val);
    },
    (err) => console.error(err)
  )
  .then(
    (val) => console.log(val),
    (err) => conosle.log(err)
  );
```

# Question 4

Convert the previous code so that it now handles errors using the catch handler

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  // return Promise.reject("error");
  return new Promise((resolve, reject) => {
    // throw "Throw Error";
    zlib.gzip(data, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
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
  .then((val) => zlibPromise(val))
  .then((val) => console.log(val))
  .catch((err) => console.error(err));
```

# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise((resolve) =>
    setTimeout(() => {
      return resolve("Finish in " + sleep);
    }, sleep)
  );
}

// readFileFake(5000); // This resolves a promise after 5 seconds, pretend it's a large file being read from disk
Promise.race([readFileFake(5000), new Promise((res, rej) => setTimeout(res, 1000, "Finish in 1000"))])
  .then((val) => console.log(val))
  .catch((err) => console.error(err));
```

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise((resolve) => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log("Publishing");
  return new Promise((resolve) => setTimeout(resolve, 1000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, "timeout"));
}

function safePublish() {
  return publish()
    .then((data) => {
      if (data.status === 403) {
        return authenticate();
      }
    })
    .then((data) => console.log("Published"));
}

Promise.race([safePublish(), timeout(3100)]).catch((err) => {
  if (err === "timeout") {
    console.log("Request timed out");
  } else {
    console.error(err);
  }
});
```
