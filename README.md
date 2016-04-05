#### Use Aria2's JSON-RPC Over WebSocket Within Docker

***

Related Reference:<p>
[*Click Here*](https://aria2.github.io/manual/en/html/aria2c.html#rpc-interface)<p>
[*And Here*](http://wenzhixin.net.cn/2013/10/27/nodejs_json_rpc_aria2)<p>

##### Precondition
+ Ubuntu 14.04 64Bit
+ Docker

##### Goal
Put Aria2 into docker enviroment, and host pc can use JSON-RPC to connect to it which can downloads files.

##### Procedure
+ Run Docker<p>
`service docker start`<p>

+ Download Ubuntu Image<p>
`(sudo) docker pull ubuntu`<p>

+ Run Ubuntu Container<p>
`(sudo) docker run -it ubuntu /bin/bash`<p>

+ Install Aria2 Into Container<p>
`apt-get update`<p>
`apt-get install aria2`<p>
*PS: The default version of installed is 1.18.1, and the newest version is 1.21.0 by 2016/03/31*<p>

+ Configure Aria2<p>
 - Create & Edit aria2.conf<p>
 `vi ~/.aria2/aria2.conf`<p>

          enable-rpc=true
          rpc-listen-all=true
          rpc-listen-port=6800
          
          max-concurrent-downloads=10
          continue=true
          max-connection-per-server=10
          min-split-size=10M
          split=10
          max-overall-download-limit=0
          max-download-limit=0
          max-overall-upload-limit=0
          max-upload-limit=0
          
          dir=/home/my_name/downloads

 - Run aria2 Server<p>
 `aria2c --conf-path=/home/my_name/.aria2/aria2.conf &`<p>

+ Encapsulate Websocket Client<p>
`nano ./websocket.js`<p>

        var WebSocketClient = require('websocket').client,
        
            client = new WebSocketClient(), 
            conn, 
            cb,
            cbmap = {};
        
        client.on('connect', function(connection) {
            console.log('INFO: WebSocket client connected to Aria2.');
            connection.on('error', function(error) {
                console.error("ERROR: Connection Error: " + error.toString());
            });
            connection.on('close', function() {
                console.log('INFO: Connection Closed');
            });
            connection.on('message', function(message) {
                if (message.type === 'utf8') {
                    var data = JSON.parse(message.utf8Data);
                    if (typeof cbmap[data.id] === 'function') {
                        var result = {
                            obj: data,
                            err: data.error ? new Error(data.error.message) : false
                        };
                        cbmap[data.id](result);
                    }
                    delete cbmap[data.id];
                }
            });
        
            conn = connection;
            if (typeof cb === 'function') {
                cb();
            }
        });
        
        client.on('connectFailed', function(error) {
            console.error('ERROR: Client Error: ' + error.toString());
        });
        
        function connect(callback) {
            cb = callback;
            client.connect('ws://localhost:6800/jsonrpc');
        }
        
        function uuid() {
            return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
                var r = Math.random() * 16 | 0, v = (c === 'x') ? r : (r & 0x3 | 0x8);
                return v.toString(16);
            });
        }
        
        function send(command, callback) {
            var id = uuid();
            if (typeof callback === 'function') {
                cbmap[id] = callback;
            }
        
            command.jsonrpc = '2.0';
            command.id = id;
            conn.sendUTF(JSON.stringify(command));
        }
        
        exports.connect = connect;
        exports.send = send;

+ Invoke Websocket Client<p>
`nano ./test.js`<p>

        var websocket = require('./websocket.js');
        
        websocket.connect(function() {
            websocket.send({
                method : 'aria2.addUri',
                params : [['http://wenzhixin.net.cn/images/header_bg.jpg']]
            }, function(result) {
                console.log(result);
            });
        });
        
`node test.js`<p>
*PS: You'll find 'header_bg.jpg' in your '/home/my_name/downloads' folder.*<p>
