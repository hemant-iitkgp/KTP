# KGP Transport Protocol (KTP)

> A custom user-level reliable transport protocol built over UDP, implementing a sliding window mechanism with shared memory and semaphores for inter-process communication.

This project was developed as a part of the Computer Networks (CS21004) course assignment at **IIT Kharagpur** by **Hemant (22CS30029)**. It demonstrates the core principles of reliable data transfer, concurrency control, and network simulation in a C-based environment.

[![Language](https://img.shields.io/badge/Language-C-blue.svg)](https://en.wikipedia.org/wiki/C_(programming_language))
[![License](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

---

## ✨ Features

-   ✅ **Reliable Data Transfer**: Implements a robust transport layer over the unreliable UDP protocol.
-   ✅ **Sliding Window Protocol**: Manages flow control and reliability using sequence numbers, acknowledgements (ACKs), and retransmissions.
-   ✅ **Simulated Packet Loss**: Probabilistically drops messages with a configurable probability `P` to test protocol resilience.
-   ✅ **Persistence Timer**: Handles zero-window deadlock scenarios by periodically sending probe packets.
-   ✅ **Multi-User Support**: Uses shared memory and semaphores to allow multiple user processes to communicate concurrently.
-   ✅ **File Transfer**: Includes example sender (`user1`) and receiver (`user2`) programs to demonstrate file transfer.
-   ✅ **Clean Resource Management**: A dedicated garbage collector thread ensures that shared memory and semaphores are properly released when user processes terminate unexpectedly.

## 📁 Repository Structure

```bash
.
├── ksocket.h          # Header: data structures and KTP API declarations
├── ksocket.c          # Implementation of k_socket, k_bind, k_sendto, k_recvfrom, k_close
├── initksocket.c      # Daemon to initialize shared memory, semaphores, and manage protocol threads
├── user1.c            # Sender application: sends file content using KTP
├── user2.c            # Receiver application: receives and writes file content using KTP
├── makefile           # Automates the build and clean process
└── Documentation.pdf  # Detailed summary of the design, implementation, and results
