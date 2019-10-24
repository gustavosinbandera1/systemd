

# Run node.js service with systemd


## Nodejs server to do the test

The below code is showing a basic nodejs server to do the test.
``` js
const http = require('http');

const hostname = '0.0.0.0'; // listen on all ports
const port = 1337;

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World\n');
}).listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});

```



# systemd
