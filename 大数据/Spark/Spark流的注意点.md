`Structure Streaming`从Kafka中读取数据处理时

对于处理时间模式：

* 如果Kafka中未产生新的数据，则控制台不会输出；
* 如果Kafka中产生新的数据，**对于未设置trigger情况**，则Kafka接收一条数据，Spark就处理一条处理；如果**设置了trigger**，则Spark会处理**trigger设置的时间间隔**中接收到的数据；



处于事件时间模式：

* 如果Kafka中未产生新的数据，则控制台不会输出；
* 如果Kafka中产生新的数据，输出的时间可能会延迟很长；



而对于`Spark Streaming`，不管Kafka是否有接收到数据，控制台总会输出信息。