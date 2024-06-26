# Quarkus demo: Hibernate Search Standalone + Elasticsearch and RESTEasy

This is a CRUD service exposing a couple of endpoints over REST,
with a front-end based on Angular so you can play with it from your browser.

While the code is surprisingly simple, under the hood this is using:
 - RESTEasy to expose the REST endpoints
 - Hibernate Search Standalone + Elasticsearch to index entities in an Elasticsearch index
 - ArC, the CDI inspired dependency injection tool with zero overhead

## Requirements

To compile and run this demo you will need:

- JDK 17+
- GraalVM

In addition, you will need an Elasticsearch instance, or Docker to run it.

### Configuring GraalVM and JDK 17+

Make sure that both the `GRAALVM_HOME` and `JAVA_HOME` environment variables have
been set, and that a JDK 17+ `java` command is on the path.

See the [Building a Native Executable guide](https://quarkus.io/guides/building-native-image)
for help setting up your environment.

## Building the demo

Launch the Maven build on the checked out sources of this demo:

> ./mvnw package

Note that running this command will start an Elasticsearch cluster and run the tests.

## Running the demo

### Live coding with Quarkus

The Maven Quarkus plugin provides a development mode that supports
live coding. To try this out:

>  ./mvnw quarkus:dev

In this mode you can make changes to the code and have the changes immediately applied, by just refreshing your browser.

    Hot reload works even when modifying your JPA entities.
    Try it! Even the Elasticsearch mapping will be updated on the fly.

### Run Quarkus in JVM mode

When you're done iterating in developer mode, you can run the application as a
conventional jar file.

First compile it:

> ./mvnw package

Note that this command will start an Elasticsearch cluster to execute the tests.
Thus your Elasticsearch containers need to be stopped.

Next we need to make sure you have an Elasticsearch instance running
(Quarkus automatically starts one for dev and test mode, but not for prod mode).

To set up an Elasticsearch instance using Docker:

> docker run -it --rm=true --name elasticsearch_quarkus_test -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:8.12.0

Then run the application:

> java -jar ./target/quarkus-app/quarkus-run.jar

    Have a look at how fast it boots.
    Or measure total native memory consumption...

### Run Quarkus as a native application

You can also create a native executable from this application without making any
source code changes. A native executable removes the dependency on the JVM:
everything needed to run the application on the target platform is included in
the executable, allowing the application to run with minimal resource overhead.

Compiling a native executable takes a bit longer, as GraalVM performs additional
steps to remove unnecessary codepaths. Use the  `native` profile to compile a
native executable:

> ./mvnw package -Dnative

After getting a cup of coffee, you'll be able to run this binary directly:

> ./target/hibernate-search-standalone-elasticsearch-quickstart-1.0.0-SNAPSHOT-runner

    Please brace yourself: don't choke on that fresh cup of coffee you just got.
    
    Now observe the time it took to boot, and remember: that time was mostly spent to generate the tables in your database and import the initial data.
    
    Next, maybe you're ready to measure how much memory this service is consuming.

N.B. This implies all dependencies have been compiled to native;
that's a whole lot of stuff.

## See the demo in your browser

Navigate to:

<http://localhost:8080/>

Have fun, and join the team of contributors!

## Running the demo on Kubernetes

To run the demo on Kubernetes, you will need to define resources:

* A `Deployment` running the application's container image.
* A `Service` and `Route` pointing to the application to expose it outside of the cluster.
* A `Deployment` or `StatefulSet` running Elasticsearch.
* A `Service` pointing to Elasticsearch to expose it to the application.

Then, make sure the `Deployment` running the application uses
environment variables that point to the Elasticsearch `Service`:

```bash
QUARKUS_HIBERNATE_SEARCH_ORM_ELASTICSEARCH_HOSTS=<ELASTICSEARCH_SERVICE_NAME>:9200
```