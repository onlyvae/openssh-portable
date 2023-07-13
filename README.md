# OpenSSH Honeypot

This is a high interaction SSH honeypot based on OpenSSH. This honeypot will record the authentication informationm, requested environment and ssh transportation content in json format which can be easily imported to other analysis tools or databases.

## Demo
![avatar](demo.gif)


## How to build

Build commands in Ubuntu 20.04:

```
apt install gcc make autoconf libssl-dev zlib1g zlib1g-dev -y
git clone https://github.com/onlyvae/openssh-portable
cd openssh-portable
autoreconf
./configure
make
```
Note: if you are using Ubuntu 22.04, then you need to build the openssl 1.1 manually. Because in Ubuntu 22.04 the default version of libssl-dev is 3.0.

## Data format
Json log file saved at /var/log/ssh.log. It contains following 9 fields:

 Field       | Note                                                                                 
-------------|--------------------------------------------------------------------------------------
 `session`   | Identify each SSH connection                                                         
 `timestamp` | Timestamp                                                                            
 `src_ip`    | Source IP                                                                            
 `src_port`  | Source Port                                                                          
 `dst_ip`    | Destination IP                                                                       
 `dst_port`  | Destination Port                                                                     
 `comment`   | Data comment. It can be "Client Version", "Password Authentication", Channel Data... 
 `channel`   | SSH Channel ID                                                                       
 `payload`   | Base64 encoded data, because there might be binary data                              


If you want to view the base64 decoded data in the terminal, you can base64 decode the `payload` field by jq. Eg:

```bash
cat /var/log/ssh.log|jq ". + {data: .payload|@base64d}
```
```json
{
  "session": "b6f67c09625a533887c8620a143e7a270",
  "timestamp": "2023-07-13T03:34:36.896119",
  "src_ip": "192.168.88.1",
  "src_port": 62303,
  "dst_ip": "192.168.88.151",
  "dst_port": 2222,
  "comment": "Client Version",
  "channel": "-1",
  "payload": "U1NILTIuMC1PcGVuU1NIXzkuMA==",
  "data": "SSH-2.0-OpenSSH_9.0"
}
{
  "session": "b6f67c09625a533887c8620a143e7a270",
  "timestamp": "2023-07-13T03:34:46.457735",
  "src_ip": "192.168.88.1",
  "src_port": 62303,
  "dst_ip": "192.168.88.151",
  "dst_port": 2222,
  "comment": "Password Authentication",
  "channel": "-1",
  "payload": "cm9vdDoxMjM0NTY3OA==",
  "data": "root:12345678"
}
{
  "session": "b6f67c09625a533887c8620a143e7a270",
  "timestamp": "2023-07-13T03:34:50.063823",
  "src_ip": "192.168.88.1",
  "src_port": 62303,
  "dst_ip": "192.168.88.151",
  "dst_port": 2222,
  "comment": "Channel Request: exec",
  "channel": "0",
  "payload": "aWQ=",
  "data": "id"
}
{
  "session": "b6f67c09625a533887c8620a143e7a270",
  "timestamp": "2023-07-13T03:34:50.069398",
  "src_ip": "192.168.88.151",
  "src_port": 2222,
  "dst_ip": "192.168.88.1",
  "dst_port": 62303,
  "comment": "Channel Data: server [session]",
  "channel": "0",
  "payload": "dWlkPTAocm9vdCkgZ2lkPTAocm9vdCkgZ3JvdXBzPTAocm9vdCkK",
  "data": "uid=0(root) gid=0(root) groups=0(root)\n"
}
```
