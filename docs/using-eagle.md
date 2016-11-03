# Overview
`Placeholder for topic: Overview`

---

# Server Configuration
`Placeholder for topic: Server Configuration`

---

# Managing Applications
`Placeholder for topic: Managing Applications`

---

# Site Management
`Placeholder for topic: Site Management`

---

# Policy and Alert

## Policy

### Create Alert Policy
`Placeholder for topic: Create Alert Policy`

---

# Troubleshooting and Debugging
`Placeholder for topic: Troubleshooting and Debugging`

---

# Monitor Eagle
`Placeholder for topic: Monitor Eagle`

---

# How to stream audit log into Kafka

## Logstash

The sample configuration is tested with logstash-2.3.4. Logstash is required to be installed on the namenode host.

* **Step 1**: Create a Kafka topic as the streaming input.

    Here is an sample Kafka command to create topic 'sandbox_hdfs_audit_log'

        cd <kafka-home>
        bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic sandbox_hdfs_audit_log

* **Step 2**: Create a Logstash configuration file under ${LOGSTASH_HOME}/conf. Here is a sample.

        input {
              file {
                  type => "hdp-nn-audit"
                  path => "/tmp/test/hdfs-audit.log"
                  start_position => end
                  sincedb_path => "/dev/null"
               }
          }
         output {
              if [type] == "hdp-nn-audit" {
                  kafka {
                    codec => plain {
                        format => "%{message}"
                    }
                    bootstrap_servers => "host:9092"
                    topic_id => "hdfs_audit_log"
                    acks => "0"
                    timeout_ms => 10000
        
                    send_buffer_bytes => 102400
                    client_id => "hdp-nn-audit"
        
                    workers => 10
                    compression_type => "gzip"
                 }
                  # stdout { codec => rubydebug }
          }
        }
        
* **Step 4**: Start Logstash

        bin/logstash -f conf/sample.conf

* **Step 5**: Check whether logs are flowing into the kafka topic specified by `topic_id`

## Filebeat 

The sample filebeat.yml is tested with filebeat-5.0.0-beta1-linux-x86_64. The throughput can be up to 20K messages per second. Filebeat is required to be installed on the namenode host.

        filebeat.publish_async: false
        filebeat.spool_size: 8192
        filebeat.idle_timeout: 5s
        max_procs: 1
        queue_size: 1000

        filebeat.prospectors:
        - input_type: log
          paths:
             - /tmp/test/hdfs-audit.log
          #tail_files: true
          harvester_buffer_size: 8192

        output.kafka:
          enabled: true
          hosts: ["host:9092"]
          topic: "phx_hdfs_audit_log"
          client_id: "client-host"
          worker: 10
          max_retries: 3
          bulk_max_size: 8192
          channel_buffer_size: 512
          timeout: 10
          broker_timeout: 3s
          keep_alive: 0
          compression: none
          max_message_bytes: 1000000
          required_acks: 0
          flush_interval: 1

        logging.metrics.period: 10s

        processors:
          - include_fields:
             fields: ["message", "beat.hostname"]

## Log4j Kafka Appender

This sample configuration is tested in HDP sandbox. `Restarting namenode is required` after updating the log4j configuration. 

* **Step 1**: Create a Kafka topic. Here is an example Kafka command for creating topic "sandbox_hdfs_audit_log"

        cd <kafka-home>
        bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic sandbox_hdfs_audit_log

* **Step 2**: Configure $HADOOP_CONF_DIR/log4j.properties, and add a log4j appender "KAFKA_HDFS_AUDIT" to hdfs audit logging

        log4j.appender.KAFKA_HDFS_AUDIT=org.apache.eagle.log4j.kafka.KafkaLog4jAppender
        log4j.appender.KAFKA_HDFS_AUDIT.Topic=sandbox_hdfs_audit_log
        log4j.appender.KAFKA_HDFS_AUDIT.BrokerList=sandbox.hortonworks.com:6667
        log4j.appender.KAFKA_HDFS_AUDIT.KeyClass=org.apache.eagle.log4j.kafka.hadoop.AuditLogKeyer
        log4j.appender.KAFKA_HDFS_AUDIT.Layout=org.apache.log4j.PatternLayout
        log4j.appender.KAFKA_HDFS_AUDIT.Layout.ConversionPattern=%d{ISO8601} %p %c{2}: %m%n
        log4j.appender.KAFKA_HDFS_AUDIT.ProducerType=async
        #log4j.appender.KAFKA_HDFS_AUDIT.BatchSize=1
        #log4j.appender.KAFKA_HDFS_AUDIT.QueueSize=1

* **Step 3**: Edit $HADOOP_CONF_DIR/hadoop-env.sh, and add the reference to KAFKA_HDFS_AUDIT to HADOOP_NAMENODE_OPTS.

        -Dhdfs.audit.logger=INFO,DRFAAUDIT,KAFKA_HDFS_AUDIT

* **Step 4**: Edit $HADOOP_CONF_DIR/hadoop-env.sh, and append the following command to it.

        export HADOOP_CLASSPATH=${HADOOP_CLASSPATH}:/path/to/eagle/lib/log4jkafka/lib/*

* **Step 5**: save the changes and restart the namenode.

* **Step 6**: Check whether logs are flowing into Topic sandbox_hdfs_audit_log

        $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic sandbox_hdfs_audit_log
