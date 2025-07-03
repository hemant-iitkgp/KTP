KGP Transport Protocol (KTP)
A custom user-level reliable transport protocol built over UDP, implementing a sliding window mechanism with shared memory and semaphores for inter-process communication.
This project was developed as a part of the Computer Networks (CS21004) course assignment at IIT Kharagpur by Hemant (22CS30029). It demonstrates the core principles of reliable data transfer, concurrency control, and network simulation in a C-based environment.
![alt text](https://img.shields.io/badge/Language-C-blue.svg)

![alt text](https://img.shields.io/badge/License-MIT-green.svg)
✨ Features
✅ Reliable Data Transfer: Implements a robust transport layer over the unreliable UDP protocol.
✅ Sliding Window Protocol: Manages flow control and reliability using sequence numbers, acknowledgements (ACKs), and retransmissions.
✅ Simulated Packet Loss: Probabilistically drops messages with a configurable probability P to test protocol resilience.
✅ Persistence Timer: Handles zero-window deadlock scenarios by periodically sending probe packets.
✅ Multi-User Support: Uses shared memory and semaphores to allow multiple user processes to communicate concurrently.
✅ File Transfer: Includes example sender (user1) and receiver (user2) programs to demonstrate file transfer.
✅ Clean Resource Management: A dedicated garbage collector thread ensures that shared memory and semaphores are properly released when user processes terminate unexpectedly.
📁 Repository Structure
Generated bash
.
├── ksocket.h          # Header: data structures and KTP API declarations
├── ksocket.c          # Implementation of k_socket, k_bind, k_sendto, k_recvfrom, k_close
├── initksocket.c      # Daemon to initialize shared memory, semaphores, and manage protocol threads
├── user1.c            # Sender application: sends file content using KTP
├── user2.c            # Receiver application: receives and writes file content using KTP
├── makefile           # Automates the build and clean process
└── Documentation.pdf  # Detailed summary of the design, implementation, and results
Use code with caution.
Bash
⚙️ How It Works
Initialization Daemon (initksocket)
The initksocket process is the core of the KTP system. It must be running before any user applications. Its primary responsibilities are:
Shared Memory: Creates shared memory segments for the global socket table and individual socket buffers.
Semaphores: Initializes semaphores for synchronizing access to shared resources between the daemon and user processes.
Protocol Threads: It spawns three long-running threads:
Receiver Thread: Listens on a well-known UDP port for all incoming KTP packets (data, ACKs, probes). It demultiplexes them to the appropriate socket buffer.
Sender Thread: Manages all outgoing transmissions. It handles packet retransmissions on timeout and sends persistence probes when the receiver's window is zero.
Garbage Collector Thread: Periodically checks for inactive sockets whose associated user processes have crashed or exited, and cleans up their resources.
User Programs (user1, user2)
user1 (Sender): Reads a file named content.txt and sends its data in 512-byte chunks using k_sendto.
user2 (Receiver): Receives data with k_recvfrom and writes the output to a file named received_<IP>_<port>.txt.
The end of a file transfer is signaled by a special termination block (\r\n.\r\n).
📋 Custom KTP API
The protocol provides a familiar socket-like API for user applications:
k_socket(): Creates a KTP socket and returns a descriptor.
k_bind(): Binds the KTP socket to a source IP/port and sets the destination IP/port.
k_sendto(): Reliably sends a block of data using the sliding window mechanism. It blocks if the send window is full.
k_recvfrom(): Reads data from the socket's receive buffer. It blocks if the buffer is empty.
k_close(): Closes the KTP socket and signals the daemon to release associated resources.
🚀 Getting Started
Follow these steps to compile and run the project.
Prerequisites
A C compiler (like gcc).
make build automation tool.
A Linux/Unix-like environment that supports POSIX shared memory and semaphores.
Compilation
To build the daemon and user programs, run:
Generated bash
make
Use code with caution.
Bash
This will create three executables: initksocket, user1, and user2.
To clean up build artifacts:
Generated bash
make clean
Use code with caution.
Bash
Running the Project
You will need three separate terminal windows.
1️⃣ Launch the Initialization Daemon
In the first terminal, start the KTP daemon. This process must be running in the background.
Generated bash
./initksocket
Use code with caution.
Bash
2️⃣ Start the Receiver (user2)
In the second terminal, run the receiver. It will wait for the sender to connect.
Generated bash
# Usage: ./user2 <receiver_IP> <receiver_port> <sender_IP> <sender_port>
./user2 127.0.0.1 6000 127.0.0.1 5000
Use code with caution.
Bash
3️⃣ Start the Sender (user1)
Before running the sender, make sure you have a file named content.txt in the same directory.
Generated bash
# Create a sample file to send
echo "Hello, KGP Transport Protocol! This is a test file for reliable data transfer." > content.txt
Use code with caution.
Bash
In the third terminal, run the sender. It will read content.txt and begin sending it to the receiver.
Generated bash
# Usage: ./user1 <sender_IP> <sender_port> <receiver_IP> <receiver_port>
./user1 127.0.0.1 5000 127.0.0.1 6000
Use code with caution.
Bash
The receiver will save the received content to received_127.0.0.1_6000.txt.
🔧 Configurable Parameters
You can adjust the protocol's behavior by modifying these constants in ksocket.h:
Generated c
// ksocket.h

#define P 0.50                      // Message drop probability (0.0 to 1.0)
#define T 5                         // Timeout interval in seconds
#define MAX_KTP_SOCKETS 25          // Maximum simultaneous sockets
#define SENDER_MSG_BUFFER 10        // Sender window size
#define RECEIVER_MSG_BUFFER 10      // Receiver window size
Use code with caution.
C
📈 Performance Results
The protocol's efficiency was tested by varying the packet drop probability (P). The table below shows the average number of transmissions required per message.
Drop Probability (P)	Average Transmissions per Message
0.05	1.00
0.10	1.00
0.20	1.29
0.35	1.49
0.50	1.86
For a more detailed analysis, please refer to the Documentation.pdf file.
