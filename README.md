# Reliable Data Transfer
### stop-and-wait implementation

Rigo Sandoval, CSCI4211S23, 3/27/2023
Java, A.java B.java,, simulator.java

## Compilation
1. Download the following files in the same directory:
    - A.java
    - B.java
    - circular_buffer.java
    - event.java
    - event_list.java
    - msg.java
    - packet.java
    - simulator.java
2. Open up the command line terminal
3. Navigate to the directory using the command line
4. Run "javac simulator.java" in the command line

## Execution/Running
1. Run "java simulator" in the command line
2. The simulator will immediately start providing the sender A with message data to send

## Description
This Java code implements the rdt3.0 stop-and-wait Reliable Data Transfer protocol. A.java contains code for the sender and B.java contains code for the receiver. The file simulator.java contains code that will simulate layer 5 sending/receiving data to the sender/receiver as well as a layer 3 channel in which the probability of a packet being dropped or corrupted can be adjusted.

### Sender Code (A.java)
The sender code is really simple. If the sender is called from layer 5 and given a message, it will create a packet using the packet structure and send the packet to the receiver via a sim.to_layer3() call. It will also start a timer to send another packet if an ACK is not received within the estimated round-trip-time.

If a packet is received by the sender, it will check if the packet received has been corrupted, or sent out-of-order. If not, it will update it's state to wait for the next message to send. If it has, it will send a NAK to the receiver.

#### Functions/Methods implemented
- **Initialization of A:**
    The sender was initialized in the "WAIT_LAYER5" state, with an outgoing packet sequence number of 0, and an estimated round-trip-time of 30 milliseconds.
- **A_output():**
    Upon receiving a message from the simulated layer 5, this function will create a packet structure with a seqnum and acknum of the current sender's sequence number. It will save a copy (lastpacket) and send the packet to the receiver via a sim.to_layer3() call, start the timer, update it's state to wait for an ACK, and update the sequence number for the next packet by alternating the "bit" from 0 to 1 or 1 to 0.
- **A_input():**
    Upon receiving a message from the receiver, this function will stop the timer, check if the packet has been corrupted, check if the ACK received is what is was expecting. If both of these conditions are met, the sender will update its state to wait for the next message to send. If the packet received was an out-of-order packet, it will resend the last packet sent and restart the timer. If the packet received was corrupted, it will send a NAK and restart the timer.
- **A_handle_timer():**
    This function resends the last packet sent, and restarts the timer.

### Receiver Code (B.java)
The receiver code is even simpler. The receiver will check if the packet received is in order and not corrupted. If it is, it will send an ACK to the sender and send to packet payload to the simulated layer 5. If not, it will send a NAK to the sender.

#### Functions/Methods implemented
- **Initialization of B:**
    The receiver is initialized to expect a packet with sequence number 0.
- **B_input():**
    This function will check if the packet received is not corrupted and expected. If so, it will send the payload of the packet to the simulated layer 5 and send an ACK to the sender. If not, it will send a NAK to the sender.

### Evaluation
#### Test Cases:
##### No lost packets, no corrupted packets
Edit simulator.java variables lossprob = (float) 0.0 and corruptprob = (float) 0.0

No lost or corrupted packets, so all messages should be received correctly
Console Output:

    recieving aaaaaaaaaaaaaaaaaaaa
    recieving bbbbbbbbbbbbbbbbbbbb
    recieving cccccccccccccccccccc
    recieving dddddddddddddddddddd
    recieving eeeeeeeeeeeeeeeeeeee
    recieving ffffffffffffffffffff
    recieving gggggggggggggggggggg
    recieving hhhhhhhhhhhhhhhhhhhh
    recieving iiiiiiiiiiiiiiiiiiii
    recieving jjjjjjjjjjjjjjjjjjjj
    recieving kkkkkkkkkkkkkkkkkkkk
    recieving llllllllllllllllllll
    recieving mmmmmmmmmmmmmmmmmmmm
    recieving nnnnnnnnnnnnnnnnnnnn
    recieving oooooooooooooooooooo
    recieving pppppppppppppppppppp
    recieving qqqqqqqqqqqqqqqqqqqq
    recieving rrrrrrrrrrrrrrrrrrrr
    recieving ssssssssssssssssssss
    The simulator has sent enough packets. Simulation end

##### Packets lost, no corrupted packets**
Edit simulator.java variables lossprob = (float) 0.2 and corruptprob = (float) 0.0

##### No lost packets, packets corrupted**
Edit simulator.java variables lossprob = (float) 0.0 and corruptprob = (float) 0.2

##### Packets lost, packets corrupted**
Edit simulator.java variables lossprob = (float) 0.2 and corruptprob = (float) 0.2
    
