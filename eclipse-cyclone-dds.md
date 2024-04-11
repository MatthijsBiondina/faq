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

### 4. Install Eclipse Iceoryx (Optional)

Eclipse Iceoryx provides mechanisms for zero-copy inter-process-communication. It is optional and not required to run Cyclone DDS. However, if desired, you might need to build it from source as packages may not be readily available for Raspberry Pi. Check the [Eclipse Iceoryx GitHub](https://github.com/eclipse-iceoryx/iceoryx) page for building instructions.

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
cmake -DCMAKE_INSTALL_PREFIX=<install-location> ..
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

# Comprehensive Manual: Using Cyclone DDS with Python Across Multiple Devices

This manual provides a step-by-step guide to using Eclipse Cyclone DDS for developing distributed applications in Python, specifically tailored for setups involving multiple devices (e.g., a network of Raspberry Pis or different computers within a lab environment).


## 1: Setting Up Cyclone DDS

Follow the installation instructions outlined previously to setup Cyclone DDS on each device you intend to use. Ensure each device has Python 3 and the `cyclonedds-python` package installed. Here’s how to install the Python package:

```bash
pip3 install cyclonedds-python
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
