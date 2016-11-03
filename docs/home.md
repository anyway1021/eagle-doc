# What is Eagle

** Apache Eagle ** (incubating) is a highly extensible, scalable monitoring and alerting platform, designed with its flexible application framework and proven big data technologies, such as Kafka, Spark and Storm. It ships a rich set of applications for big data platform monitoring, e.g. HDFS/HBase/YARN service health check, JMX metrics, daemon logs, audit logs and yarn applications. External Eagle developers can define applications to monitoring their NoSQLs or Web Servers, and publish to Eagle application repository at your own discretion. It also provides the state-of-art alert engine to report security breaches, service failures, and application anomalies, highly customizable by the alert policy definition. 

![overview](include/images/overview.png)

---

# Example Use Cases

## Data Activity Monitoring

* Data activity represents how user explores data provided by big data platforms. Analyzing data activity and alerting for insecure access are fundamental requirements for securing enterprise data. As data volume is increasing exponentially with Hadoop, Hive, Spark technology, understanding data activities for every user becomes extremely hard, let alone to alert for a single malicious event in real time among petabytes streaming data per day.

* Securing enterprise data starts from understanding data activities for every user. Apache Eagle (incubating, called Eagle in the following) has integrated with many popular big data platforms e.g. Hadoop, Hive, Spark, Cassandra etc. With Eagle user can browse data hierarchy, mark sensitive data and then create comprehensive policy to alert for insecure data access.

## Job Performance Analysis

* Running map/reduce job is the most popular way people use to analyze data in Hadoop system. Analyzing job performance and providing tuning suggestions are critical for Hadoop system stability, job SLA and resource usage etc.

* Eagle analyzes job performance with two complementing approaches. First Eagle periodically takes snapshots for all running jobs with YARN API, secondly Eagle continuously reads job lifecycle events immediately after the job is completed. With the two approaches, Eagle can analyze single job's trend, data skew problem, failure reasons etc. More interestingly, Eagle can analyze whole Hadoop cluster's performance by taking into account all jobs.

## Cluster Performance Analytics

* It is critical to understand why a cluster performs bad. Is that because of some crazy jobs recently on-boarded, or huge amount of tiny files, or namenode performance degrading?

* Eagle in realtime calculates resource usage per minute out of individual jobs, e.g. CPU, memory, HDFS IO bytes, HDFS IO numOps etc. and also collects namenode JMX metrics. Correlating them together will easily help system administrator find root cause for cluster slowness.

---

# Disclaimer

Apache Eagle now is being incubated, and therefore, across the whole documentation site, all appearances of case-insensitive word `eagle` and `apache eagle` represent `Apache Eagle (incubating)`. This could be seen as a part of disclaimer.
