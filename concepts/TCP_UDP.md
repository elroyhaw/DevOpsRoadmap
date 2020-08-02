# Transmission Control Protocol (TCP) & User Datagram Protocol (UDP)
## Introduction
- Transport layer (layer 4) protocol
- A machine/container has an IP address which is used to address the machine/container
- But a machine/container can be running thousands of applications
- Therefore, need a further discriminator to address the applications using ports 

## TCP
### Pros
- Acknowledgement from server (that packet is delivered)
- Guaranteed delivery (through retransmission)
- Connection based (client and server need to have a unique connection between them - stateful connection)
- Congestion control
- Ordered packets

### Cons
- Larger packets
  - Extra information for supporting the features mentioned in Pros
- More bandwidth
    - Because packets are larger
- Slower than UDP
    - Because need to wait for the features mentioned in Pros
- Stateful
  - Easy way to think of stateless: if you can restart a server, and client can resume his work with no issues (server doesn't store information about connection)
- Server memory
  - Server has to allocate memory for each connection
  - Finite number of TCP connections per server, may cause Denial Of Service (DOS) when a bad client opens many TCP connections and doesn't respond to server, causing server to wait and allocate memory to these connections

### Code
```python
import socket

HOST = '127.0.0.1'
PORT = 8080

# TCP sockets created with socket.SOCK_STREAM
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
	s.bind((HOST, PORT))
	s.listen()
	conn, addr = s.accept()
	with conn:
		print(f"Connected by {addr}")
		while True:
			data = conn.recv(1024)
			if not data:
				break
			print(data)
```
```bash
telnet 127.0.0.1 8080
```

## UDP
### Pros
- Smaller packets
- Less bandwidth
  - Some games uses reliable UDP (which implements some of the Cons of UDP)
- Faster than TCP
- Stateless

### Cons
- No acknowledgement from server
- No guaranteed delivery
- Connectionless
- No congestion control
- No ordered packets
- Security (anyone can send data to port that is opened if he knows IP and port)

### Code
```python
import socket

HOST = '127.0.0.1'
PORT = 8080
BUFFER_SIZE = 1024

# UDP sockets created with socket.SOCK_DGRAM
with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
	s.bind((HOST, PORT))
	data = s.recvfrom(BUFFER_SIZE)
	while data:
		print(f"Received {data[0]} from {data[1]}")
		data = s.recvfrom(BUFFER_SIZE)
```
```bash
# -w1 timeouts netcat after 1 second
echo "test" | nc -w1 -u 127.0.0.1 8080
```