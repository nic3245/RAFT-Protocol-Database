# RAFT-Protocol-Database
A from-scratch python database that implements the RAFT protocol.
# Description
To read about the protocol itself, the paper and descriptions are located at: [Raft Github](https://raft.github.io/) <br>
This protocol values Consistency and Availability of the CAP Theorem.
# Running the Program
The command line specification is as follows: <br>
```$ ./3700kvstore <UDP port> <your ID> <ID of second replica> [<ID of third replica> ...]] ```<br>
Thus, the IDs for the replicas need to be established beforehand.
# Using the database
The multiple replicas will take inputs via the UPD port, and these inputs will follow the format below: <br>
get: get() messages are read requests from clients. They have the following format: <br>

```{"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "get", "MID": "<a unique string>", "key": "<some key>"} ```<br>
The replica will respond with an "ok" value of the following format: <br>

``` {"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "ok", "MID": "<a unique string>", "value": "<value of the key>"} ```
 <br>
Or a "fail" message, in which case the client should retry the get(): <br>

```{"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "fail", "MID": "<a unique string>"}``` <br>
If the client issues a get() for a key that has does not exist (i.e., it was never put()), the replica will return an ok message with an empty value (i.e., an empty string). <br>

put: put() messages are write requests from clients. They have the following format: <br>

```{"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "put", "MID": "<a unique string>", "key": "<some key>", "value": "<value of the key>"}``` <br>
The replica will respond with an ok message if the write was successful: <br>

```{"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "ok", "MID": "<a unique string>"} ```<br>
Or with a fail message, in which case the client should retry the put(): <br>

```{"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "fail", "MID": "<a unique string>"} ```<br>
redirect: If the client sends any message (get() or put()) to a replica that is not the leader, the replica will respond with a redirect: <br>

```{"src": "<ID>", "dst": "<ID>", "leader": "<ID>", "type": "redirect", "MID": "<a unique string>"} ```<br>
In this case, the client will retry the request by sending it to the specified leader. <br>

Note that in all of the above cases, the MID in a request will match the MID in the response.
