# Architecture
`Placeholder for topic: Architecture`

# Concepts
`Placeholder for topic: Concepts`

# Introduction
`Placeholder for topic: Introduction`

# Use Cases
`Placeholder for topic: Use Cases`

# Quick Start

## Deployment

### Prerequisites

Eagle requires the following dependencies:

* For streaming platform dependencies
    * Storm: 0.9.3 or later
    * Hadoop: 2.6.x or later
    * Hbase: 0.98.x or later
    * Kafka: 0.8.x or later
    * Zookeeper: 3.4.6 or later
    * Java: 1.8.x
* For metadata database dependencies (Choose one of them)
    * MangoDB 3.2.2 or later
        * Installation is required
    * Mysql 5.1.x or later
        * Installation is required

Notice:

    Storm 0.9.x does NOT support JDK8. You can replace asm-4.0.jar with asm-all-5.0.jar in the storm lib directory. 
    Then restart other services(nimbus/ui/supervisor). 

### Installation

##### Build Eagle

* Download the latest version of Eagle source code.

        git clone https://github.com/apache/incubator-eagle.git
        
* Build the source code, and a tar.gz package will be generated under eagle-server-assembly/target

        mvn clean install -DskipTests.
        
##### Deploy Eagle

* Copy binary package to your server machine. In the package, you should find

		bin/: scripts used for start eagle server
        conf/: default configurations for eagle server setup.
        lib/ : all included software packages for eagle server


* Change configurations under conf/
	* eagle.conf
    * server.yml
        	
* Run eagle-server.sh

        ./bin/eagle-server.sh start
        
* Check eagle server

        In your web browser, visit http://host:port/

## Setup Your Monitoring Case
`Placeholder for topic: Setup Your Monitoring Case`