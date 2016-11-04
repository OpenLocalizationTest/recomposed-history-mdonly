---
title: An introduction to Apache Kafka on HDInsight | Microsoft Docs
description: ''
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2016
ms.author: larryfr
---
# Introducing Apache Kafka on HDInsight
[Apache Kafka](https://kafka.apache.org) is a open-source distributed streaming platform that can be used to build real-time streaming data pipelines and applications. Kafka also provides message broker functionality similar to a message queue, where you can publish and subscribe to named data streams. Kafka on HDInsight provides you with a managed, highly scalable and highly available service in the Microsoft Azure cloud.

## Why use Kafka on HDInsight?
Kafka provides the following features:

* Publish-subscribe messaging pattern: Kafka provides a Producer API for publishing records to a Kafka topic. The Consumer API is used when subscribing to a topic.
* Stream processing: Kafka is often used with Apache Storm or Spark for real-time stream processing. Kafka 0.10.0.0 (HDInsight version 3.5) introduced a streaming API that allows you to build streaming solutions without requiring Storm or Spark.
* Horizontal scale: Kafka partitions streams across the nodes in the HDInsight cluster. Consumer processes can be associated with individual partitions to provide load balancing when consuming records.
* In-order delivery: Within each partition, records are stored in the stream in the order that they were received. By associating one consumer process per partition, you can guarantee that records are processed in-order.
* Fault-tolerant: Partitions can be replicated between nodes to provide fault tolerance.

## Use cases
* **Messaging**: Since it supports the publish-subscribe message pattern, Kafka is often used as a message broker.
* **Activity tracking**: Since Kafka provides in-order logging of records, it can be used to track and re-create activities. For example, user actions on a web site or within an application.
* **Aggregation**: Using stream processing, you can aggregate information from different streams to combine and centralize the information into operational data.
* **Transformation**: Using stream processing, you can combine and enrich data from multiple input topics into one or more output topics.

## Where do I start?
See [Get started with Kafka on HDInsight](hdinsight-apache-kafka-get-started.md) for steps on creating a Kafka cluster and using Kafka, including Java-based examples of using the producer, consumer, and streaming API

## Next steps
Use the following links to learn how to use Apache Kafka on HDInsight:

* [Get started with Kafka on HDInsight](hdinsight-apache-kafka-get-started.md)
* [Use MirrorMaker to create a replica of Kafka on HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md)

