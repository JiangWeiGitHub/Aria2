#### Use Aria2's JSON-RPC Over WebSocket Within Docker

***

Related Reference: [*Click Here*](https://aria2.github.io/manual/en/html/aria2c.html#rpc-interface)

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

 - Run aria2c<p>
 `aria2c --conf-path=/home/my_name/.aria2/aria2.conf &`<p>
