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
`(sudo) docker pull Ubuntu`<p>

+ Run Ubuntu Container<p>
`(sudo) docker run -it Ubuntu /bin/bash`<p>

+ Install Aria2 Into Container<p>
`apt-get install aria2`<p>
*PS: The default version of installed is 1.18.1, and the newest version is 1.21.0 by 2016/03/31*
