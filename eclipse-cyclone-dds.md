# Eclipse Cyclone DDS Documentation

### Index
1. [What is DDS?](#what-is-dds)
2. [Why Cyclone DDS?](#why-cyclone-dds)
3. [Installation](#installation)
   - [Update Your System](#1-update-your-system)
   - [Install C Compiler and Related Tools](#2-install-c-compiler-and-related-tools)
   - [Install OpenSSL (Optional)](#3-install-openssl-optional)
   - [Skipping Iceoryx Installation for Now](#4-skipping-iceoryx-installation-for-now)
   - [Install Bison (Optional)](#5-install-bison-optional)
   - [Check Installation of Prerequisites](#6-check-installation-of-prerequisites)
   - [Clone Cyclone DDS Repository](#7-clone-cyclone-dds-repository)
   - [Proceed with Cyclone DDS Installation](#8-proceed-with-cyclone-dds-installation)
   - [Install Cyclone DDS](#9-install-cyclone-dds)
4. [Using Cyclone DDS with Python Across Multiple Devices](#using-cyclone-dds-with-python-across-multiple-devices)
   - [Setting Up Cyclone DDS](#1-setting-up-cyclone-dds)
   - [Basic Concepts of Cyclone DDS](#2-basic-concepts-of-cyclone-dds)
   - [A Simple Example](#3-a-simple-example)
   - [Running the Example](#4-running-the-example)
   - [Advanced Configuration](#5-advanced-configuration)
   - [Scaling Up](#6-scaling-up)
5. [Using Shared Memory Transport in Cyclone DDS with Python](#using-shared-memory-transport-in-cyclone-dds-with-python)
   - [Introduction to Shared Memory Transport](#1-introduction-to-shared-memory-transport)
   - [Setting Up the Environment](#2-setting-up-the-environment)
   - [Configuring Cyclone DDS for Shared Memory](#3-configuring-cyclone-dds-for-shared-memory)
   - [Implementing Shared Memory Transport in Python](#4-implementing-shared-memory-transport-in-python)
   - [Running Your Application](#5-running-your-application)
6. [Implementing Services and Actions Using Cyclone DDS](#implementing-services-and-actions-using-cyclone-dds)
   - [Introduction to Services and Actions](#1-introduction-to-services-and-actions)
   - [Implementing Services](#3-implementing-services)
   - [Implementing Actions](#4-implementing-actions)
7. [Advanced QoS Settings for Cyclone DDS](#advanced-qos-settings-for-cyclone-dds)
   - [Introduction to QoS in DDS](#1-introduction-to-qos-in-dds)
   - [Key QoS Policies](#2-key-qos-policies)
   - [Combining Multiple QoS Policies](#3-combining-multiple-qos-policies)
8. [Setting Up Cyclone DDS for PyCharm](#5-setting-up-cyclone-dds-for-pycharm) 

---

# Eclipse Cyclone DDS

### What is DDS?

The Data Distribution Service (DDS) is a middleware protocol and API standard for data-centric connectivity. It provides a scalable, flexible solution for real-time machine-to-machine communication. DDS is widely used in systems requiring high reliability, complex data distribution, and real-time performance.

### Why Cyclone DDS?

Eclipse Cyclone DDS is an open-source implementation of the DDS specification that provides a robust, scalable, and efficient backend for distributed systems. It is particularly favored in the robotics and IoT domains due to its performance metrics and ease of integration with technologies like ROS 2.

## Installation

Setting up Eclipse Cyclone DDS on a Raspberry Pi running Ubuntu 20.04 involves ensuring that you have all the necessary prerequisites installed. Below are the detailed steps to check and install these prerequisites:

### 1. Update Your System

First, make sure your system package list and the system itself are up-to-date:

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Install C Compiler and Related Tools

Cyclone DDS requires a C compiler (GCC), CMake, and potentially other build tools. Install these along with GIT for version control:

```bash
sudo apt install build-essential cmake git -y
```

- **build-essential**: Installs GCC and other essential compilers and libraries.
- **cmake**: A system that manages the build process in an operating system and in a compiler-independent manner.
- **git**: Version control system used to manage the Cyclone DDS source code.

### 3. Install OpenSSL (Optional)

OpenSSL is optional unless you require TLS/TCP support and security features. Install it with:

```bash
sudo apt install openssl libssl-dev -y
```

- **openssl**: The basic binary for running OpenSSL.
- **libssl-dev**: Development libraries for compiling applications that use OpenSSL.

### 4. Skipping Iceoryx Installation for Now

Although the official instructions suggest installing Iceoryx during this step for shared memory transport, this can cause build errors in some cases. It’s recommended to skip installing Iceoryx at this point and complete the Cyclone DDS installation first. You can revisit Iceoryx installation after Cyclone DDS is set up and working.

### 5. Install Bison (Optional)

Bison is a general-purpose parser generator. If you plan to modify or develop parsers within Cyclone DDS, install Bison with:

```bash
sudo apt install bison -y
```

### 6. Check Installation of Prerequisites

After installation, you can verify the versions installed (which can be helpful for troubleshooting):

```bash
gcc --version
cmake --version
git --version
openssl version
bison --version
```

Each command should return the version of the respective tool installed, confirming their presence on your system.

### 7. Clone Cyclone DDS Repository

Once all prerequisites are installed, you can clone the Cyclone DDS repository:

```bash
git clone https://github.com/eclipse-cyclonedds/cyclonedds.git
cd cyclonedds
```

### 8. Proceed with Cyclone DDS Installation

Create a build directory and proceed with the configuration:

```bash
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DENABLE_ICEORYX=OFF ..
cmake --build .
```

Replace `<install-location>` with the directory where you want Cyclone DDS installed (e.g., `/usr/local`).

### 9. Install Cyclone DDS

To install Cyclone DDS on your Raspberry Pi:

```bash
sudo cmake --build . --target install
```

This command installs Cyclone DDS to the directory specified earlier.

### Conclusion

You now have all the prerequisites installed on your Raspberry Pi for developing with Eclipse Cyclone DDS. You can begin setting up your development environment, compiling, and running DDS applications. If you encounter issues, consider verifying each component's installation, checking log files, or consulting documentation specific to each tool.

# Using Cyclone DDS with Python Across Multiple Devices

This manual provides a step-by-step guide to using Eclipse Cyclone DDS for developing distributed applications in Python, specifically tailored for setups involving multiple devices (e.g., a network of Raspberry Pis or different computers within a lab environment).


## 1: Setting Up Cyclone DDS

Follow the installation instructions outlined previously to setup Cyclone DDS on each device you intend to use. Ensure each device has Python 3 and the `cyclonedds` package installed. Here’s how to install the Python package:

```bash
export CYCLONEDDS_HOME=/usr/local
pip3 install git+https://github.com/eclipse-cyclonedds/cyclonedds-python
```

## 2: Basic Concepts of Cyclone DDS

### Domain Participants

A DomainParticipant creates an entry point to the DDS network and acts as a factory for creating Topics, Publishers, and Subscribers.

### Topics

A Topic is a category under which data is published and subscribed. Think of it as a news channel that conveys specific types of news.

### Data Writers and Readers

- **DataWriter**: Publishes data under a topic.
- **DataReader**: Subscribes to a topic to receive its data.

### Quality of Service (QoS)

QoS settings allow fine-grained control over various aspects of data distribution, such as reliability and latency.

## 3: A Simple Example

### Python Script for a Publisher

Here is a basic publisher script that sends a "Hello" message. Run this on the first device (Publisher Device).

```python
from dataclasses import dataclass
from cyclonedds.domain import DomainParticipant
from cyclonedds.pub import Publisher, DataWriter
from cyclonedds.topic import Topic
from cyclonedds.core import Qos, Policy
from time import sleep

@dataclass
class HelloMessage:
    index: int
    message: str

# Initialize a DomainParticipant
participant = DomainParticipant()

# Create a Topic
topic = Topic(participant, "HelloTopic", HelloMessage)

# Initialize a Publisher
publisher = Publisher(participant)

# Initialize a DataWriter with QoS settings for reliability
data_writer = DataWriter(publisher, topic, qos=Qos(Policy.Reliability.Reliable()))

# Send data continuously
count = 0
while True:
    msg = HelloMessage(index=count, message="Hello from Publisher!")
    data_writer.write(msg)
    print(f"Sent: {msg}")
    count += 1
    sleep(1)  # Send a message every second
```

### Python Script for a Subscriber

Run this on the second device (Subscriber Device).

```python
from dataclasses import dataclass
from cyclonedds.domain import DomainParticipant
from cyclonedds.sub import Subscriber, DataReader
from cyclonedds.topic import Topic
from cyclonedds.core import Qos, Policy

@dataclass
class HelloMessage:
    index: int
    message: str

# Initialize a DomainParticipant
participant = DomainParticipant()

# Create a Topic
topic = Topic(participant, "HelloTopic", HelloMessage)

# Initialize a Subscriber
subscriber = Subscriber(participant)

# Initialize a DataReader with QoS settings for reliability
data_reader = DataReader(subscriber, topic, qos=Qos(Policy.Reliability.Reliable()))

# Read data continuously
while True:
    for data in data_reader.take():
        print(f"Received: {data}")
```

## 4: Running the Example

### Setup Network

Ensure all devices are connected to the same network. Configure firewalls to allow UDP traffic which DDS uses for data transmission.

### Execute the Programs

1. **On the Publisher Device**: Run the publisher script.
2. **On the Subscriber Device**: Run the subscriber script.

Data published by the publisher device should appear on the subscriber device's console, indicating that the setup is correct.

## 5: Advanced Configuration

Explore advanced QoS policies like Deadline, Lifespan, or Latency Budget to fine-tune the performance according to your network environment and application requirements.

## 6: Scaling Up

To scale this setup:
- Introduce more publishers or subscribers.
- Implement different topics for categorized data streams.
- Utilize hierarchical domain structures for large-scale deployments.

## Conclusion

This manual provides the necessary steps and example code to get started with Cyclone DDS using Python across multiple devices

. By following these guidelines, developers can build robust, distributed applications tailored to specific needs of real-time data distribution.

# Using Shared Memory Transport in Cyclone DDS with Python

This manual offers a detailed guide to using shared memory transport with Eclipse Cyclone DDS, leveraging the Eclipse Iceoryx middleware for Python applications. Shared memory transport is particularly useful for high-performance applications on single machines, such as real-time robotic control systems, where latency and throughput are critical.

## 1: Introduction to Shared Memory Transport

### What is Shared Memory Transport?

Shared memory transport allows inter-process communication (IPC) without the overhead of passing data through the kernel, which decreases latency and increases throughput. It is ideal for applications that require fast, efficient data exchange between multiple processes on the same machine.

### Benefits of Eclipse Iceoryx

Eclipse Iceoryx is a middleware that facilitates zero-copy data transfer via shared memory, providing:

- **Zero-Copy Data Transfer**: Data is not copied between sender and receiver; both have access to a common memory location.
- **Low Latency**: Significantly reduces the time taken for data transfer, essential for real-time applications.
- **High Throughput**: Increases the data rates that can be achieved over IPC.

## 2: Setting Up the Environment

### Prerequisites

1. **Operating System**: Linux-based OS is recommended (e.g., Ubuntu).
2. **Hardware**: Any modern CPU with support for shared memory.
3. **Software**:
   - Python 3.6 or newer.
   - CMake 3.16 or higher.
   - GCC or Clang compiler.
   - Git.

### Installing Dependencies

#### Install Cyclone DDS

Follow the standard installation steps for Cyclone DDS, ensuring you have cloned the Cyclone DDS repository and installed necessary tools like CMake and GCC.

#### Install Eclipse Iceoryx

1. **Clone the Iceoryx Repository**:

```bash
git clone https://github.com/eclipse-iceoryx/iceoryx.git
cd iceoryx
```

2. **Build Iceoryx**:

```bash
mkdir build && cd build
cmake ..
make
sudo make install
```

3. **Setup Iceoryx Environment** (add to your `.bashrc` or `.bash_profile`):

```bash
source ~/iceoryx/build/iceoryx_env_setup.bash
```

## 3: Configuring Cyclone DDS for Shared Memory

### Enabling Iceoryx in Cyclone DDS

When building Cyclone DDS, enable the Iceoryx plugin:

```bash
cmake -DENABLE_ICEORYX=ON ..
make
sudo make install
```

### Configure Cyclone DDS XML

Modify the Cyclone DDS configuration XML to enable shared memory transport:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<CycloneDDS xmlns="https://cdds.io/config" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://cdds.io/config https://raw.githubusercontent.com/eclipse-cyclonedds/cyclonedds/master/etc/cyclonedds.xsd">
  <Domain>
    <General>
      <SharedMemory>
        <Enable>true</Enable>
      </SharedMemory>
    </General>
  </Domain>
</CycloneDDS>
```

Set the environment variable to point to this configuration file:

```bash
export CYCLONEDDS_URI=file:///path/to/cyclonedds.xml
```

## 4: Implementing Shared Memory Transport in Python

### Basic Setup

Create Python scripts for both a publisher and a subscriber that use the shared memory transport.

#### Publisher Script

```python
from dataclasses import dataclass
from cyclonedds.domain import DomainParticipant
from cyclonedds.pub import DataWriter
from cyclonedds.topic import Topic
from cyclonedds.core import Qos, Policy
import time

@dataclass
class Message:
    id: int
    content: str

participant = DomainParticipant()
topic = Topic(participant, "SharedMemoryTopic", Message)
writer = DataWriter(participant, topic, Qos(Policy.Reliability.Reliable()))

for i in range(100):
    msg = Message(id=i, content="Hello through shared memory!")
    writer.write(msg)
    print(f"Published: {msg}")
    time.sleep(1)
```

#### Subscriber Script

```python
from dataclasses import dataclass
from cyclonedds.domain import DomainParticipant
from cyclonedds.sub import DataReader
from cyclonedds.topic import Topic
from cyclonedds.core import Qos, Policy

@dataclass
class Message:
    id: int
    content: str

participant = DomainParticipant()
topic = Topic(participant, "SharedMemoryTopic", Message)
reader = DataReader(participant

, topic, Qos(Policy.Reliability.Reliable()))

while True:
    for data in reader.take():
        print(f"Received: {data}")
```

## 5: Running Your Application

1. **Open two terminals**: one for the publisher and one for the subscriber.
2. **Run the scripts**: Ensure both scripts are executable and the Cyclone DDS environment is properly configured to use Iceoryx for shared memory transport.

## 6: Troubleshooting

- **Permissions**: Ensure you have the appropriate permissions to access shared memory segments.
- **Iceoryx Compatibility**: Make sure that Iceoryx is compatible with your hardware and OS version.
- **Environmental Variables**: Verify that all necessary environmental variables (`CYCLONEDDS_URI`, `Iceoryx` setup) are correctly configured.

## Conclusion

By following this manual, you should be able to set up and use shared memory transport with Eclipse Cyclone DDS and Iceoryx in Python, facilitating high-performance IPC suitable for real-time applications. This setup not only optimizes data transfer speeds and efficiency but also provides a robust solution adaptable to complex distributed systems.

# Implementing Services and Actions Using Cyclone DDS

This manual provides detailed guidance on setting up and using services and actions in distributed systems using Eclipse Cyclone DDS, specifically with Python bindings. This functionality is akin to what is provided by ROS services and actions, but it's implemented directly through the DDS infrastructure.

## 1: Introduction to Services and Actions

### Services

Services in the context of DDS are implemented through a Request/Reply pattern:
- **Requester (Client)**: Sends a request and waits for a reply.
- **Replier (Server)**: Receives the request, processes it, and sends back a reply.

### Actions

Actions are more complex and are designed for long-running asynchronous tasks:
- **Goal**: Client sends a goal to the server.
- **Feedback**: Server processes the goal and may send feedback periodically.
- **Result**: Once the action is complete, the server sends a result back to the client.

## 2: Setting Up Your Environment

Before implementing services and actions, ensure Cyclone DDS and Python bindings are properly installed on all participating devices. Follow the installation guide provided earlier to set up Cyclone DDS, and install the Python package using:

```bash
pip3 install cyclonedds cyclonedds-tools
```

## 3: Implementing Services

Implementing services involves creating a requester and a replier. Below are examples for each.

### Service Definition

First, define the data types for the request and reply. For this example, we'll create a simple service to add two integers.

```python
# Define data classes for the request and reply
from dataclasses import dataclass

@dataclass
class AddTwoIntsRequest:
    a: int
    b: int

@dataclass
class AddTwoIntsResponse:
    sum: int
```

### Replier (Server)

The server listens for requests, processes them, and sends responses.

```python
from cyclonedds.domain import DomainParticipant
from cyclonedds.ser import Service
from cyclonedds.util import duration

def add_two_ints(request):
    result = request.a + request.b
    return AddTwoIntsResponse(sum=result)

# Setup Domain Participant
participant = DomainParticipant()

# Create a service
service = Service(participant, "AddTwoInts", AddTwoIntsRequest, AddTwoIntsResponse)

# Run service to handle requests
service.run(add_two_ints)
```

### Requester (Client)

The client sends requests to the server and waits for responses.

```python
from cyclonedds.domain import DomainParticipant
from cyclonedds.ser import Client
from time import sleep

# Setup Domain Participant
participant = DomainParticipant()

# Create a client
client = Client(participant, "AddTwoInts", AddTwoIntsRequest, AddTwoIntsResponse)

# Send a request
request = AddTwoIntsRequest(a=10, b=20)
response = client.request(request)
print(f"Received response: {response.sum}")
```

## 4: Implementing Actions

Actions are implemented similarly to services but are designed for tasks that take longer to complete and may require continuous feedback.

### Action Definition

Define the data types for the goal, feedback, and result.

```python
@dataclass
class ComputeFactorialGoal:
    number: int

@dataclass
class ComputeFactorialFeedback:
    current_result: int
    percent_complete: float

@dataclass
class ComputeFactorialResult:
    factorial: int
```

### Action Server

This server processes goals and sends feedback and results.

```python
from cyclonedds.ser import ActionServer
import time

def compute_factorial(goal, feedback_publisher):
    result = 1
    for i in range(1, goal.number + 1):
        result *= i
        feedback = ComputeFactorialFeedback(current_result=result, percent_complete=(i/goal.number)*100)
        feedback_publisher.publish(feedback)
        time.sleep(0.5)  # Simulate a time-consuming task
    return ComputeFactorialResult(factorial=result)

# Setup Domain Participant
participant = DomainParticipant()

# Create an action server
action_server = ActionServer(participant, "ComputeFactorial",
                             ComputeFactorialGoal, ComputeFactorialFeedback, ComputeFactorialResult)

# Run action server to handle goals
action_server.run(compute_factorial)
```

### Action Client

The client sends goals and receives feedback and results.

```python
from cyclonedds.ser import ActionClient

# Setup Domain Participant
participant = DomainParticipant()

# Create an action client
action_client = ActionClient(participant, "ComputeFactorial",
                             ComputeFactorialGoal, ComputeFactorialFeedback, ComputeFactorialResult)

# Send a goal and handle feedback and result
goal = ComputeFactorialGoal(number=5)
result = action_client.send_goal(goal, feedback_callback=lambda fb: print(f"Feedback

: {fb.current_result}, {fb.percent_complete}% complete"))

print(f"Received result: {result.factorial}")
```

## 5: Running the Examples

To run these examples:
1. Ensure all scripts are saved and the environment for each device is correctly configured.
2. Start the server or action server script on one device.
3. Run the client or action client script on another device or the same device in a different terminal.

These scripts will allow two-way communication using services and perform long-running tasks with continuous feedback using actions.

## Conclusion

This manual covers the basic and some advanced uses of Cyclone DDS for implementing distributed services and actions using Python. By following these instructions, you can set up a robust communication system suitable for a wide array of applications in robotics, IoT, and beyond.

# Advanced QoS Settings for Cyclone DDS

This manual delves into the advanced Quality of Service (QoS) settings available in Cyclone DDS when using Python. Understanding and applying these QoS settings correctly is crucial for optimizing DDS applications, especially in environments that require reliable, timely, and efficient data handling.

## 1: Introduction to QoS in DDS

Quality of Service (QoS) policies in DDS provide granular control over the behavior of data distribution, enabling developers to specify how data should be handled across the network. These policies can be applied to entities such as data writers, data readers, and topics.

## 2: Key QoS Policies

Here’s an overview of the most relevant and commonly used advanced QoS policies in DDS:

### Reliability

Controls the guarantee of payload delivery.

- **Best Effort**: The delivery of the message is not guaranteed; messages may be lost if the network is congested.
- **Reliable**: Guarantees delivery of messages; ensures all data is received even if some packets are dropped.

**Example Usage**:

```python
from cyclonedds.domain import DomainParticipant
from cyclonedds.pub import Publisher, DataWriter
from cyclonedds.sub import Subscriber, DataReader
from cyclonedds.topic import Topic
from cyclonedds.core import Qos, Policy
from dataclasses import dataclass

@dataclass
class Message:
    content: str

participant = DomainParticipant()
topic = Topic(participant, "ExampleTopic", Message)

publisher = Publisher(participant)
writer_qos = Qos(Policy.Reliability.Reliable())
data_writer = DataWriter(publisher, topic, qos=writer_qos)

subscriber = Subscriber(participant)
reader_qos = Qos(Policy.Reliability.Reliable())
data_reader = DataReader(subscriber, topic, qos=reader_qos)
```

### Deadline

Specifies the maximum expected interval between consecutive data samples.

**Example Usage**:

```python
from datetime import timedelta

# Setting a deadline period of 2 seconds
deadline_qos = Qos(Policy.Deadline(timedelta(seconds=2)))

data_writer = DataWriter(publisher, topic, qos=deadline_qos)
data_reader = DataReader(subscriber, topic, qos=deadline_qos)
```

### Durability

Specifies the lifecycle of a data instance.

- **Volatile**: The data is not stored after being published.
- **Transient Local**: The data is stored by the DataWriter and available to late-joining DataReaders.
- **Transient**: Data is stored until it is explicitly deleted, potentially even surviving the DataWriter that wrote it.

**Example Usage**:

```python
durability_qos = Qos(Policy.Durability.TransientLocal())

data_writer = DataWriter(publisher, topic, qos=durability_qos)
data_reader = DataReader(subscriber, topic, qos=durability_qos)
```

### Liveliness

Manages the lifecycle of entities on the network, determining how and when they are considered "alive."

**Example Usage**:

```python
# Liveliness lease duration set to 10 seconds
liveliness_qos = Qos(Policy.Liveliness.LeaseDuration(timedelta(seconds=10)))

data_writer = DataWriter(publisher, topic, qos=liveliness_qos)
data_reader = DataReader(subscriber, topic, qos=liveliness_qos)
```

### History

Controls the behavior of the history cache.

- **Keep Last**: Only stores a limited number of the most recent values.
- **Keep All**: Stores all values.

**Example Usage**:

```python
# Keeping the last 10 samples
history_qos = Qos(Policy.History.KeepLast(10))

data_writer = DataWriter(publisher, topic, qos=history_qos)
data_reader = DataReader(subscriber, topic, qos=history_qos)
```

## 3: Combining Multiple QoS Policies

Multiple QoS policies can be combined to fine-tune the behavior of Cyclone DDS entities further. Here is an example of combining reliability, deadline, and history QoS settings:

```python
combined_qos = Qos(
    Policy.Reliability.Reliable(),
    Policy.Deadline(timedelta(seconds=2)),
    Policy.History.KeepLast(20)
)

data_writer = DataWriter(publisher, topic, qos=combined_qos)
data_reader = DataReader(subscriber, topic, qos=combined_qos)
```

## 4: Practical Application Example

Consider a scenario where sensor data is being sent from multiple IoT devices and must be processed by a central system. The data is critical, so we need to ensure it's reliably transmitted, processed within specific time frames, and the history is adequately maintained for late analysis.
# Eclipse Cyclone DDS Documentation

### Index
1. [What is DDS?](#what-is-dds)
2. [Why Cyclone DDS?](#why-cyclone-dds)
3. [Installation](#installation)
   - [Update Your System](#1-update-your-system)
   - [Install C Compiler and Related Tools](#2-install-c-compiler-and-related-tools)
   - [Install OpenSSL (Optional)](#3-install-openssl-optional)
   - [Skipping Iceoryx Installation for Now](#4-skipping-iceoryx-installation-for-now)
   - [Install Bison (Optional)](#5-install-bison-optional)
   - [Check Installation of Prerequisites](#6-check-installation-of-prerequisites)
   - [Clone Cyclone DDS Repository](#7-clone-cyclone-dds-repository)
   - [Proceed with Cyclone DDS Installation](#8-proceed-with-cyclone-dds-installation)
   - [Install Cyclone DDS](#9-install-cyclone-dds)
4. [Using Cyclone DDS with Python Across Multiple Devices](#using-cyclone-dds-with-python-across-multiple-devices)
   - [Setting Up Cyclone DDS](#1-setting-up-cyclone-dds)
   - [Basic Concepts of Cyclone DDS](#2-basic-concepts-of-cyclone-dds)
   - [A Simple Example](#3-a-simple-example)
   - [Running the Example](#4-running-the-example)
   - [Advanced Configuration](#5-advanced-configuration)
   - [Scaling Up](#6-scaling-up)
5. [Using Shared Memory Transport in Cyclone DDS with Python](#using-shared-memory-transport-in-cyclone-dds-with-python)
   - [Introduction to Shared Memory Transport](#1-introduction-to-shared-memory-transport)
   - [Setting Up the Environment](#2-setting-up-the-environment)
   - [Configuring Cyclone DDS for Shared Memory](#3-configuring-cyclone-dds-for-shared-memory)
   - [Implementing Shared Memory Transport in Python](#4-implementing-shared-memory-transport-in-python)
   - [Running Your Application](#5-running-your-application)
6. [Implementing Services and Actions Using Cyclone DDS](#implementing-services-and-actions-using-cyclone-dds)
   - [Introduction to Services and Actions](#1-introduction-to-services-and-actions)
   - [Implementing Services](#3-implementing-services)
   - [Implementing Actions](#4-implementing-actions)
7. [Advanced QoS Settings for Cyclone DDS](#advanced-qos-settings-for-cyclone-dds)
   - [Introduction to QoS in DDS](#1-introduction-to-qos-in-dds)
   - [Key QoS Policies](#2-key-qos-policies)
   - [Combining Multiple QoS Policies](#3-combining-multiple-qos-policies)
8. [Setting Up Cyclone DDS for PyCharm](#5-setting-up-cyclone-dds-for-pycharm) 

---
### Device (Publisher) Side Script:

```python
# Sensor data publication script
@dataclass
class SensorData:
    timestamp: float
    value: float

sensor_topic = Topic(participant,

 "SensorData", SensorData)
sensor_data_writer = DataWriter(publisher, sensor_topic, qos=combined_qos)

import time
while True:
    data = SensorData(timestamp=time.time(), value=get_sensor_value())
    sensor_data_writer.write(data)
    time.sleep(1)  # Simulating data collection frequency
```

### Central System (Subscriber) Side Script:

```python
# Sensor data collection script
sensor_data_reader = DataReader(subscriber, sensor_topic, qos=combined_qos)

while True:
    for data in sensor_data_reader.take():
        process_sensor_data(data)
```

## 5: Setting Up Cyclone DDS for PyCharm

PyCharm's debugger doesn't automatically have access to environment variables, so you need to create a `.env` file to define them explicitly. Follow these steps to configure your Cyclone DDS setup for PyCharm:

1. **Create a `.env` file**: This file will contain the necessary environment variables for Cyclone DDS. Here's an example of what your `.env` file should look like:

   ```bash
   CYCLONEDDS_HOME=/usr/local
   CYCLONEDDS_URI="path/to/cyclonedds.xml"
   ```

   Replace `path/to/cyclonedds.xml` with the actual path to your Cyclone DDS configuration XML file.

2. **Configure PyCharm to use the `.env` file**:
   - Open **Run/Debug Configurations** in PyCharm.
   - Select your configuration or create a new one for your Python project.
   - Under **Environment** settings, locate **Environment variables** and click the folder icon next to it.
   - In the pop-up, select **Load environment variables from file** and choose the `.env` file you created.
   
This setup ensures that your PyCharm environment has access to the necessary Cyclone DDS variables for running and debugging your project.
