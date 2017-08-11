# local

When running a Spark Streaming program locally, do not use “local” or “local\[1\]” as the master URL. Either of these means that only one thread will be used for running tasks locally. If you are using an input DStream based on a receiver \(e.g. sockets, Kafka, Flume, etc.\), then the single thread will be used to run the receiver, leaving no thread for processing the received data. Hence, when running locally, always use “local\[n\]” as the master URL, where n &gt; number of receivers to run \(see Spark Properties for information on how to set the master\).





