

# Run node.js service with systemd


## Nodejs server to do the test

The below code is showing a basic nodejs server to do the test.

We can create a folder in /opt pathe with the the next command.

```
sudo mkdir -p /opt/nodejsServer
sudo touch /opt/nodeServer/server.js
sudo vim /opt/nodeServer/server.js

```
and paste the below code.
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
We can check if all is fine runnig the next comand.
```
node /opt/nodeserver/server.js
Server running at http://0.0.0.0:1337/
```


# systemd
## Create the service file

```
sudo touch /etc/systemd/system/nodeserver.service
```

```
[Unit]
Description=Node.js Example Server
#Requires=After=mysql.service       # Requires the mysql service to run first

[Service]
ExecStart=/usr/local/bin/node /opt/nodeserver/server.js
# Required on some systems
#WorkingDirectory=/opt/nodeserver
Restart=always
# Restart service after 10 seconds if node service crashes
RestartSec=10
# Output to syslog
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=nodejs-example
#User=<alternate user>
#Group=<alternate group>
Environment=NODE_ENV=production PORT=1337

[Install]
WantedBy=multi-user.target

```

## Enable the service

```
systemctl enable nodeserver.service

```

## Verify it is running

```
systemctl status nodeserver.service
```



## Security / testing

Of course this service would run as root, which you probably shouldn't be doing, so you can edit /etc/systemd/system/nodeserver.service to run it as a different user depending on your system. If you make any changes to the service file, you will need to do a

```
systemctl daemon-reload
```

before reloading the service:
```
systemctl restart nodeserver.service
```

## Testing if the application is able to reload

Now finally we can test how systemd restarts the process if it unexpectedly dies (thanks Mark for the suggestion). We will manually "kill" the running process by finding its Process ID (pid), and note how systemd automatically restart it again:

```
ps -ef | grep server.js     # find the current pid
kill 12345                  # kill the process by its pid reported above
ps -ef | grep server.js     # notice node process is immediately respawned with a different pid

```