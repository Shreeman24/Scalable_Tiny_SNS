# Scalable Tiny SNS - Cloud Computing
We will implement Google Remote Procedure Calls (GRPC) to enable client-server-coordinator interactions where clients issue commands like post, follow, unfollow, list, timeline, and the server responds appropriately.
The projects are executed on Oracle VM VirtualBox with Linux VM on local machine.

* Project 1: Develops a client-server system for a social media messaging service, showcasing proficiency in AWS and Python.

* Project 2: Extends Project 1 by implementing a multi-server architecture with a centralized coordinator for efficient client-server routing and management.

## Project 1: Single Server Social Media Messaging Service

**Description:**

Project 1 develops a client-server system that simulates a social media messaging service. The client offers a user-friendly interface with various functionalities to interact with the server, including sending and receiving messages. The server manages client instructions, updating files, and its database accordingly.

**Key Features:**

- **gRPC Communication:** Utilizes gRPC stub for efficient remote procedure calls (RPCs), abstracting away network communication and data serialization complexities.
  
- **Server-Side Functions:** Clients can remotely execute server-side functions, enabling flexible deployment scenarios where the client can be executed on multiple terminals while the server runs on a single instance.

- **Protocol Buffers:** Definitions specified in the `sns.proto` facilitate easy implementation in C++. Upon running the `make` command, relevant C++ class implementations (e.g., `sns.h`) are generated, including getters, setters, constructors, and destructors.

- **Message Struct:** Defines a strict communication format between the server and client, streamlining message handling and ensuring uniformity.

- **Timeline Mode:** Establishes bidirectional RPC streams for persistent client-server connections. Enables functionalities such as sending messages, retrieving latest posts, and broadcasting updates to followers' timelines.

- **Server Persistence:** Implements mechanisms to parse and format client messages for server persistence. This includes converting timestamp types and writing to local files using file streams.

**Usage:**

1. **Compilation:**
    ```bash
    make
    ```
    Compiles the code using the provided makefile.

2. **Clear Directory:**
    ```bash
    make clean
    ```
    Clears the directory and removes .txt files.

3. **Run Server without Glog Messages:**
    ```bash
    ./tsd <-p port>
    ```
    Runs the server without Glog messages. The port number is optional.

4. **Run Server with Glog Messages:**
    ```bash
    GLOG_logtostderr=1 ./tsd <-p port>
    ```
    Runs the server with Glog messages. The port number is optional.

5. **Run Client without Glog Messages:**
    ```bash
    ./tsc <-h host_addr -p port> -u user1
    ```
    Runs the client without Glog messages. Host address and port are optional, while the username is required.

6. **Run Client with Glog Messages:**
    ```bash
    GLOG_logtostderr=1 ./tsc <-h host_addr -p port> -u user1
    ```
    Runs the client with Glog messages. Host address and port are optional, while the username is required.

## Project 2: Multi-Server Social Media Messaging Service

**Overview:**

Project 2 extends the functionalities developed in Project 1, enabling the connection of multiple clients to multiple servers. Unlike Project 1, which allows connection to a single server, Project 2 introduces a centralized coordinator responsible for routing each client to the correct server based on a user ID calculation.

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

**Usage:**

1. **Compile the Code:**
    ```bash
    make
    ```
    Compiles the code using the provided makefile.

2. **Clear Directory (Remove .txt Files):**
    ```bash
    make clean
    ```
    Clears the directory and removes .txt files.

3. **Run Server (Without Glog Messages):**
    ```bash
    ./tsd -c <clusterId> -s <serverId> -h <coordinatorIP> -k <coordinatorPort> -p <portNum>
    ```
    Runs the server without Glog messages. The port number is optional.
    Example:
    ```bash
    ./tsd -c 1 -s 1 -h localhost -k 9090 -p 10000
    ```

4. **Run Server (With Glog Messages):**
    ```bash
    GLOG_logtostderr=1 ./tsd -c <clusterId> -s <serverId> -h <coordinatorIP> -k <coordinatorPort> -p <portNum>
    ```
    Runs the server with Glog messages. The port number is optional.

5. **Run Client:**
    ```bash
    ./tsc -h <coordinatorIP> -k <coordinatorPort> -u <userId>
    ```
    Runs the client. The client contacts the coordinator to get the endpoint to connect to. The user ID must be specified.
    Example:
    ```bash
    ./tsc -h localhost -k 9090 -u 1
    ```

6. **Run Coordinator:**
    ```bash
    ./coordinator -p <portNum>
    ```
    Runs the coordinator. The port number is optional.
    Example:
    ```bash
    ./coordinator-p 9090
    ```

All output/logging on Servers, Coordinator, Synchronizer, and Clients are logged using the glog logging library. Logs by default reside in the `/tmp` directory.
