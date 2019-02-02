---
title: spring_kafka_json_serde
date: 2019-02-02 21:56:05
tags: 
  - kafka
  - spring cloud
  - serialization
  - deserialization
  - Java Money
---

# Deserialization problems with Java money

The Spring frameworks helps with a lot of Java's overhead configuration. Most of the time things just magically work. But sometimes not.

Spring uses Jackson for the serialization and deserialization of objects to and from json. It is very powerful and extendable. The serialized output can placed as a value or payload in a Kafka message.
