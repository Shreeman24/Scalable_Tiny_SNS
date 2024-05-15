# CSCE 412 - Cloud Computing Projects

## Project 1: Single Server Social Media Messaging Service

**Description:**

MP1 involves the development of a client-server system that simulates a social media messaging service. The client offers a user-friendly interface with various functionalities to interact with the server, including sending and receiving messages. The server manages client instructions, updating files, and its database accordingly.

**Key Features:**

- **gRPC Communication:** Utilizes gRPC stub for efficient remote procedure calls (RPCs), abstracting away network communication and data serialization complexities.
  
- **Server-Side Functions:** Clients can remotely execute server-side functions, enabling flexible deployment scenarios where the client can be executed on multiple terminals while the server runs on a single instance.

- **Protocol Buffers:** Definitions specified in `sns.proto` facilitate easy implementation in C++. Upon running the `make` command, relevant C++ class implementations (e.g., `sns.h`) are generated, including getters, setters, constructors, and destructors.

- **Message Struct:** Defines a strict communication format between the server and client, streamlining message handling and ensuring uniformity.

- **Timeline Mode:** Establishes bidirectional RPC streams for persistent client-server connections. Enables functionalities such as sending messages, retrieving latest posts, and broadcasting updates to followers' timelines.

- **Server Persistence:** Implements mechanisms to parse and format client messages for server persistence. This includes converting timestamp types and writing to local files using file streams.

**Usage:**

1. **Clone Repository:** Clone the repository to your local machine.

   ```bash
   git clone https://github.com/your_username/your_repository.git
   ```

2. **Build and Run:** Execute the `make` command to build C++ implementations and run the server. Run clients on terminals to interact with the server.

   ```bash
   cd your_repository
   make
   ./server
   ./client
   ```

## Project 2: Multi-Server Social Media Messaging Service

**Description:**
Certainly! Here's the modified README information for Project 2 using ** for section headings:

---

**CSCE 412 - Cloud Computing Project 2: Multi-Server Social Media Messaging Service**

**Overview:**

Project 2 extends the functionalities developed in Project 1, enabling the connection of multiple clients to multiple servers. Unlike Project 1, which allowed connection to a single server, Project 2 introduces a centralized coordinator responsible for routing each client to the correct server based on a calculation involving the user ID.

**Key Additions:**

- **Centralized Coordinator**: A centralized coordinator is introduced to route clients to appropriate servers based on user ID calculations.

- **Enhanced Client Functionality**: Clients now obtain the coordinator's IP and port from the user to create a separate Coordinator Service stub. This stub allows for the invocation of server-side functions in the coordinator service, facilitating server routing.

- **Server Selection**: The coordinator's **GetServer** function assigns clients to servers by populating server information from its cluster database, ensuring clients connect to the correct server.

- **Heartbeat Monitoring**: Implemented a thread-safe Heartbeat function on the coordinator site to update a server's entry in the coordinator's database. This function monitors server uptime and downtime.

**Implementation Details:**

**Coordinator Functions:**

- **Heartbeat Function**: A thread-safe function updates a server's entry in the coordinator's database, ensuring server uptime is accurately monitored. Mutex locks are implemented to safeguard sensitive server database information during updates.

- **GetServer Function**: Assigns clients to servers by populating server information from the cluster database, ensuring clients connect to the correct server.

- **RunServer Function**: Initiates server execution and monitors server downtime by calling the thread function **checkHeartbeat**.

**Server Functions:**

- **Heartbeat Mechanism**: Introduces a server mutex for thread-safe function calls, especially for heartbeat monitoring. Servers send heartbeat messages to the coordinator at regular intervals to indicate their active status.

- **RunServer Function**: Accepts additional arguments for coordinator IP, coordinator port, cluster ID, and port number. Executes the server and initiates heartbeat monitoring with a fixed interval of 1 second between heartbeats.
