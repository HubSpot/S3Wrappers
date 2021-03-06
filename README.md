# S3Decorators

## Overview

This library allows you to decorate an `AmazonS3` client in order to intercept all calls and add behavior. Potential use-cases include:
- adding a circuit-breaker to S3 calls to fail fast in the event of an outage
- injecting failures or latency for testing
- capturing metrics around S3 call durations and responses
- debug logging of all S3 calls

## Usage

To implement your own decorator, you can add the following dependency:

```xml
<dependency>
  <groupId>com.hubspot</groupId>
  <artifactId>s3-decorators-core</artifactId>
  <version>0.7</version>
</dependency>
```

And then extend `S3Decorator`. Or to get started more quickly, you can use one of the prebuilt decorators described below.

## Hystrix

You can use the `HystrixS3Decorator` in order to easily wrap all S3 calls in a Hystrix command. Use the following Maven dependency:

```xml
<dependency>
  <groupId>com.hubspot</groupId>
  <artifactId>hystrix-s3-decorator</artifactId>
  <version>0.7</version>
</dependency>
```

And then when constructing your S3 client, decorate it like so:

```java
AmazonS3 s3 = ... // construct client normally
return HystrixS3Decorator.decorate(s3);
```

We provide a default Hystrix `Setter` with a command key based on the AWS Region the client is pointing at, or you can provide your own by doing:

```java
AmazonS3 s3 = ... // construct client normally
Setter setter = ... // construct desired Setter
return HystrixS3Decorator.decorate(s3, setter);
```

## Failsafe

You can use the `FailsafeS3Decorator` in order to easily wrap all S3 calls with a Failsafe circuit-breaker. Use the following Maven dependency:

```xml
<dependency>
  <groupId>com.hubspot</groupId>
  <artifactId>failsafe-s3-decorator</artifactId>
  <version>0.7</version>
</dependency>
```

And then when constructing your S3 client, decorate it like so:

```java
AmazonS3 s3 = ... // construct client normally
return FailsafeS3Decorator.decorate(s3);
```

We provide a default `CircuitBreaker` or you can provide your own by doing:

```java
AmazonS3 s3 = ... // construct client normally
CircuitBreaker circuitBreaker = ... // construct desired CircuitBreaker
return FailsafeS3Decorator.decorate(s3, circuitBreaker);
```

## Metrics

If your app uses Dropwizard Metrics, you can use the `MetricsS3Decorator` in order to easily capture S3 call durations and failure rates. Use the following Maven dependency:

```xml
<dependency>
  <groupId>com.hubspot</groupId>
  <artifactId>metrics-s3-decorator</artifactId>
  <version>0.7</version>
</dependency>
```

And then when constructing your S3 client, decorate it like so:

```java
AmazonS3 s3 = ... // construct client normally
MetricRegistry registry = ... // the desired metric registry
return MetricsS3Decorator.decorate(s3, registry);
```

This will create a timer for request duration: 
- `com.hubspot.s3.metrics.MetricsS3Decorator.requests`

As well as a meter for exceptions:
- `com.hubspot.s3.metrics.MetricsS3Decorator.exceptions`
