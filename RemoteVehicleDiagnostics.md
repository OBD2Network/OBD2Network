# Remote Vehicle Diagnostics

OBD-II to Network 

*The current state of the project.*

## Hardware components:
As a part of our project, we use an ECUsim 2000 OBD board simulating an engine control unit
(ECU) of a car. The simulator allows testing different modes of CAN bus operation: CAN ID 11
bit or 29 bit, baud rate 250 kbps or 500 kbps.
For remote monitoring of the simulated ECU, we utilize an ELM327-based car scanner and a
free OBD-II scanner app for Android.
Two Korlan USB2CAN adapters with galvanic isolation allow us to set up network
communication over the CAN bus.

## Working with the CAN bus over the Internet
Simulator tests have revealed that CAN frames can be forwarded smoothly to the car scanner
over a local area network within the same building.

However, when it comes to transferring data over long distances across the Internet, some data
may not be received by the OBD scanner due to network delays.

It’s been detected that the OBD scanner is almost insensitive to the delays when reading single
responses to its requests (one CAN frame request - one CAN frame response). However, the
device becomes highly sensitive to network delays when reading multi-frame CAN messages
(transmitted as ISO-TP sequence of CAN frames).

[![Video Demonstration](https://i9.ytimg.com/vi/mgq-dW_Hslo/mq2.jpg?sqp=CPi1348G&rs=AOn4CLDHsBeAbyiKhj8TZFaXacmuZ-cFGw)](https://youtu.be/mgq-dW_Hslo)

According to our research, the situation with multi-frame messages gets more critical if using
Wi-Fi networks and improves when using a cable Internet connection.
Connections through the FlexiHub Tunnel Server are not recommended due to increased
latency.

The analysis we did shows that Internet delays in data transmission do not lead to the loss of
data integrity but affect the operation of applications working with CAN adapters.

Thus, we believe that the modification of dedicated software, designed to communicate with
CAN interfaces directly, may resolve the problem. The increased timeout for receiving a
response from the ECU may be an effective solution.

### ISO-TP support
As a workaround for the network delay problem, in our project, we implemented support for the
ISO-TP protocol. Smart support for this protocol allowed us to overcome the limitations of the
ELM327 car scanner utility. By using a special algorithm, the multi-frame response to the
scanner request may now be sent as a single message.

## Testing on real cars
The product we are working on has been tested on several real vehicles from different
manufacturers, including Volkswagen, Opel, Honda, Peugeot, and Toyota. Most of the ECUs we
tested were set to work with the CAN parameters 11 bits/ 500 kbps.
Our tests have shown that the amount of data transmitted over a CAN interface of a real car is
larger compared to a simulated ECU. Increased data traffic may lead to higher latency, which
sometimes results in problems with data processing on the side of the ODB scanner.
Filtering unnecessary CAN traffic is seen as a possible solution.
At this stage, the implementation of the traffic filtering functionality requires more extensive
testing and analysis of CAN data.


## Conclusion

● To access the CAN interface over the Internet, you need a high-speed direct Internet
connection. We recommend using a computer with a public IP address.

● The increase in the timeout for receiving CAN data by OBD scanner software is highly
recommended. Typical delays on the Internet can reach 10-50 ms within one city, and
300-500 ms when setting up communication between different continents.

● Smart support for CAN ISO-TP can significantly improve the compatibility with existing
car scanners, but in some cases still cannot solve the problem completely.

● To set up a CAN connection in a real car, it is recommended to use a high-performance
laptop or a notebook.

● Filtering out unnecessary CAN frames will improve CAN bus communications over the
Internet. The implementation of traffic filtering capability requires cooperation with the
customer on collecting data during operations with different cars.
