Kafka 0.11.0.0 (Confluent 3.3.0) added support to manipulate offsets for a consumer group via cli `kafka-consumer-groups` command.

1. List the topics to which the group is subscribed
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --describe
```
Note the values under "CURRENT-OFFSET" and "LOG-END-OFFSET". "CURRENT-OFFSET" is the offset where this consumer group is currently at in each of the partitions.

2. Reset the consumer offset for a topic (preview)
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --to-earliest
```
This will print the expected result of the reset, but not actually run it.

3. Reset the consumer offset for a topic (execute)
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --to-earliest --execute
```
This will execute the reset and reset the consumer group offset for the specified topic back to 0.

4. Repeat 1 to check if the reset is successful

### Note
* The consumer group must have no running instance when performing the reset. Otherwise the reset will be rejected.
* There are many other resetting options, run `kafka-consumer-groups` for details
  * --shift-by <positive_or_negative_integer>
  * --to-current
  * --to-latest
  * --to-offset <offset_integer>
  * --to-datetime <datetime_string>
  * --by-duration <duration_string>
* The command also provides an option to reset offsets for all topics the consumer group subscribes to: `--all-topics`


## Kafka Topics
### List existing topics
 `bin/kafka-topics.sh --zookeeper localhost:2181 --list`

### Describe a topic
  `bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic mytopic `
### Purge a topic
 `bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic mytopic --config retention.ms=1000`
 
... wait a minute ...

 `bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic mytopic --delete-config retention.ms`
 
### Delete a topic
 `bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic mytopic`
 
### Get number of messages in a topic ???
 `bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic mytopic --time -1 --offsets 1 | awk -F  ":" '{sum += $3} END {print sum}'`
 
### Get the earliest offset still in a topic
`bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic mytopic --time -2`

### Get the latest offset still in a topic
`bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic mytopic --time -1`

### Consume messages with the console consumer
`bin/kafka-console-consumer.sh --new-consumer --bootstrap-server localhost:9092 --topic mytopic --from-beginning`

## Get the consumer offsets for a topic
`bin/kafka-consumer-offset-checker.sh --zookeeper=localhost:2181 --topic=mytopic --group=my_consumer_group`

### Read from __consumer_offsets

Add the following property to `config/consumer.properties`:
`exclude.internal.topics=false`

`bin/kafka-console-consumer.sh --consumer.config config/consumer.properties --from-beginning --topic __consumer_offsets --zookeeper localhost:2181 --formatter "kafka.coordinator.GroupMetadataManager\$OffsetsMessageFormatter"`

## Kafka Consumer Groups

### List the consumer groups known to Kafka
`bin/kafka-consumer-groups.sh --zookeeper localhost:2181 --list`  (old api)

`bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server localhost:9092 --list` (new api)

### View the details of a consumer group 
`bin/kafka-consumer-groups.sh --zookeeper localhost:2181 --describe --group <group name>`

## kafkacat

### Getting the last five message of a topic
`kafkacat -C -b localhost:9092 -t mytopic -p 0 -o -5 -e`

## Zookeeper

### Starting the Zookeeper Shell

`bin/zookeeper-shell.sh localhost:2181`
