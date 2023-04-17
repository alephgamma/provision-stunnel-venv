# provision-stunnel-venv

This is an ansible environment to provision a basic point-to-point STUNNEL connection between to end-points. In other terms, it is an TLS/SSL tunnel for services that do not natively support TLS/SSL. Another way to understand STUNNELs is that the client connects to the server on localhost port 2222 which is then encrypted and tunneled to the server over port 3129 and then dropped onto a process on the server listening on port 3128. They key concept is that STUNNELs operate at the TCP port level (layer 4) and conceptually are "port stitching" from a process on the client to the server.

The image below is for visualization.
![alt text](https://github.com/alephgamma/provision-stunnel-venv/blob/main/provision-stunnel-venv.png?raw=true)
