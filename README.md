# avro-orc-conversion-presentation
Information on my Avro to ORC Conversion presentation

## Standalone Conversion Project

See
[jeff303/avro-orc-standalone-converter-streamsets](https://github.com/jeff303/avro-orc-standalone-converter-streamsets)
for the standalone Avro to ORC conversion app shown in the demo.

## Hadoop Cluster

To create your own Cloudera Distribution of Hadoop cluster similar to the one shown in the demo, you can use
[Clusterdock](https://clusterdock.readthedocs.io/en/latest/).  Specifically, you will use the
[streamsets branch of the CDH Clusterdock topology](https://github.com/clusterdock/topology_cdh/tree/streamsets).  Make
sure to follow all instructions, including those for how to build the CDH Docker images.  Following these steps,
and then spinning up the cluster, will give you all the Hadoop services (HDFS, MapReduce, etc.) and Data Collector
running as a Cloudera parcel.  Into that instance, you can import the sample (random data) -> Avro -> ORC pipeline
and run it yourself.

Note that you need a fairly powerful machine to run even a two-node CDH cluster.  We recommend using an EC2
instance that has at least 8+ CPUs, 32 GB+ RAM, and 256GB disk.  From there, you can establish a SOCKS proxy
to access the Data Collector instance running on the cluster.  For example, the following will establish a SOCKS
proxy:

`ssh -qi ~/path-to-my-keypair.pem -D1082 ubuntu@${MY_EC2_INSTANCE}`

Then, you can use a browser extension (such as SwitchyOmega) to connect to the Data Collector instance via
`http://node-1.cluster:18630` (default login `admin`/`admin`).

### Environment Setup
Use the following environment setup for demo purposes.  Feel free to add them to `~/.bashrc` on `node-1.cluster`

```
# run Hadoop commands as hdfs (the HDFS super-user)
# very insecure in a production environment
export HADOOP_USER_NAME=hdfs
export JAVA_HOME=$(echo /usr/java/jdk*1.8*)
```

## MapReduce Example Pipeline

Import the `Create_Avro_and_ORC_Files.json` file into your running Data Collector instance.

## Hive

If you follow the instructions above for getting a Hadoop cluster running, you should be able to run the `hive` command
from `node-1.cluster` and execute the example Hive DDL commands from the presentation.

## Spark

If you follow the instructions above for getting a Hadoop cluster running, you should be able to run the `spark-shell`
command from `node-1.cluster` and execute the example Spark commands from the presentation.  You need to include the
`--packages` argument to load the `spark-avro` plugin, in order to read Avro files.  So the full invocation would
look something like the following:

`spark-shell --packages org.apache.spark:spark-avro_2.11:2.4.1`

Note that in this example, the Scala version is `2.11`, and the Spark version is `2.4.1`.  Both of these parts of the
package version need to match with your installed Scala and Spark versions, or things won't work.

Also note that the latest version of Spark bundled with CDH 5.x is 2.3, and `spark-avro` requires Spark 2.4 or greater.
Therefore, you should use a CDH 6.1 cluster, or else install Spark on a different machine to experiment with these
(Spark can be installed standalone on a Mac with Homebrew using `brew install apache-spark`, for example)

## Command Line Tools

### Avro

There is an `avro-tools` utility for working with Avro files.  See
[the Avro website](https://avro.apache.org/docs/1.8.2/gettingstartedjava.html) for more information on that.  These
can also be installed on Mac with Homebrew using `brew install avro-tools`

### ORC

There is an `orc-tools` utility for working with ORC files.  See
[the ORC website](https://orc.apache.org/docs/java-tools.html) for more information.  These can also be installed on Mac
with Homebrew using `brew install orc-tools`
