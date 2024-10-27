## Kafka Producers
A producer is the one which publishes or writes data to the topics within different partitions. Producers automatically know that, what data should be written to which partition and broker. The user does not require to specify the broker and the partition.

## How does the producer write data to the cluster?
A producer uses following strategie//s to write data to the cluster:
- Message Keys
- Acknowledgment

# Apache Kafka Message Keys

## Overview

In Apache Kafka, message keys play a crucial role in determining how messages are distributed among partitions in a topic. This document explains the concept of message keys and their impact on message ordering and load balancing.

## Message Keys

- **With a Key**: 
  - When a producer sends a message with a key (like a string or number), Kafka routes that message to a specific partition.
  - This ensures that all messages with the same key are processed in the same order.

- **Without a Key**:
  - If no key is specified, Kafka evenly distributes messages across all partitions using a round-robin approach.
  - This is known as **load balancing**.

## Benefits

- **Ordering**: Ensures that messages with the same key maintain their order in processing.
- **Load Balancing**: Distributes messages evenly across partitions when no key is provided, optimizing resource utilization.

## Conclusion

Using message keys in Kafka helps maintain order for specific data and improves load balancing when necessary. By strategically using keys, producers can control message distribution effectively.


There are two ways to know that the data is sent with or without a key:

1. If the value of key=NULL, it means that the data is sent without a key. Thus, it will be distributed in a round-robin manner (i.e., distributed to each partition).
2. f the value of the key!=NULL, it means the key is attached with the data, and thus all messages will always be delivered to the same partition.


## Let's see an example

1. Consider a scenario where a producer writes data to the Kafka cluster, and the data is written without specifying the key. So, the data gets distributed among each partition of Topic-T under each broker, i.e., Broker 1, Broker2, and Broker 3.
![one](images/one.png)

2. Consider another scenario where a producer specifies a key as Prod_id. So, data of Prod_id_1(say) will always be sent to partition 0 under Broker 1, and data of Prod_id_2 will always be in partition 1 under Broker 2. Thus, the data will not be distributed to each partition after applying the key (as saw in the above scenario).
![two](images/two.png)


# Apache Kafka Acknowledgments

## Overview

In Apache Kafka, acknowledgments (acks) help producers confirm that their messages have been successfully written to the cluster. This document explains the different acknowledgment levels available to producers.

## Acknowledgment Options

1. **acks=0**:
   - The producer sends data to the broker without waiting for any acknowledgment.
   - This can result in data loss since the producer does not know if the message was received.

2. **acks=1**:
   - The producer waits for an acknowledgment from the leader broker.
   - The leader confirms the receipt of the data, allowing for limited data loss if the leader fails after acknowledgment.

3. **acks=all** (or `acks=-1`):
   - The producer waits for acknowledgments from both the leader and all its follower brokers.
   - This ensures that the message is safely stored across the cluster, resulting in no data loss.

## Conclusion

Choosing the appropriate acknowledgment level in Kafka is crucial for balancing data reliability and performance. By understanding these options, producers can make informed decisions on how to handle message delivery and confirmation.

Suppose, a producer writes data to Broker1, Broker 2, and Broker 3.

1. Case1: Producer sends data to each of the Broker, but not receiving any acknowledgment. Therefore, there can be a severe data loss, and the correct data could not be conveyed to the consumers.

![case1](images/c1.png)

2. Case2: The producers send data to the brokers. Broker 1 holds the leader. Thus, the leader asks Broker 1 whether it has successfully received data. After receiving the Broker's confirmation, the leader sends the feedback to the Producer with ack=1.

![case2](images/c2.png)

3. Case3: The producers send data to each broker. Now, the leader and its replica/ISR will ask their respective brokers about the data. Finally, acknowledge the producer with the feedback.
![case3](images/c3.png)
