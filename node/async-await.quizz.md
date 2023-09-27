# Question 1

Convert the promise version of the multi-file loader over to using async/await

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

const files = ["./files/demofile.txt", "./files/demofile.other.txt"];

let promises = files.map((name) => readFile(name, { encoding: "utf8" }));

// Promise.all(promises).then(values => {
//   // <-- Uses .all
//   console.log(values);
// });

(async function () {
  const files = await Promise.all(promises);
  console.log(files);
})();
```

# Question 2

Again convert the promise version of the multi-file loader over to using async/await but using a custom async iterator with the following syntax

node --harmony-async-iteration <file.js>

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

const fileIterator = (files) => ({
  [Symbol.asyncIterator]: () => ({
    x: 0,
    next() {
      // TODO
      const file = files[this.x++];

      if (this.x <= files.length) {
        return readFile(file, "utf-8").then((data) => ({
          done: false,
          value: data,
        }));
        // return Promise.resolve({
        // done: false,
        // value: files[this.x++],
        // });
      } else {
        return Promise.resolve({
          done: true,
        });
      }
    },
  }),
});

(async () => {
  for await (let x of fileIterator(["./files/demofile.txt", "./files/demofile.other.txt"])) {
    console.log(x);
  }
})();
```
