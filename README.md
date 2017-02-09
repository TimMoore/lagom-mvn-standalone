# lagom-mvn-standalone

A basic example of building a Lagom project to run outside of the Lightbend Production Suite (aka ConductR).

This was initially created from the Lagom Java Maven archetype (`mvn archetype:generate -DarchetypeGroupId=com.lightbend.lagom -DarchetypeArtifactId=maven-archetype-lagom-java -DarchetypeVersion=1.2.2`), with the following changes:

* Removed ConductR-specific configuration
* Added basic start scripts, based on the template provided in [Lagom's "Using ConductR with Maven" documentation](http://www.lagomframework.com/documentation/1.3.x/java/ConductRMaven.html) 
* Modified the assembly descriptors to create a simplified layout, with the start script directly in `bin` and all library dependencies in `lib`
* Added `ConfigurationServiceLocator` to Guice modules in each implementation project, as described in the [Lagom documentation](http://www.lagomframework.com/documentation/1.3.x/java/ProductionOverview.html)
* Added static port bindings to each service (`hello-impl`: 8001, `stream-impl`: 8002)
* Added a static service location to `hello-impl` for `cas_native` (the Cassandra server)
* Added a static service location to `stream-impl` for `hello`

To test this:

 1. Open a terminal and clone the repository to your workstation (`git clone https://github.com/TimMoore/lagom-mvn-standalone.git`)
 2. `cd lagom-mvn-standalone`
 3. `mvn package` to create distribution packages
 4. Start a Cassandra server on port 4000. There are a few ways to do this:
    1. Get an sbt-based Lagom project (such as [`lagom-sbt-standalone`](https://github.com/TimMoore/lagom-sbt-standalone)) and run `sbt lagomCassandraStart` in its root directory
    2. Run Cassandra in Docker: `docker run --rm --name cassandra -p4000:9042 -d cassandra:latest`
    3. Download and run Cassandra standalone as described in the [Cassandra Getting Started guide](http://cassandra.apache.org/doc/latest/getting_started/installing.html)
    
       _Note that you'll need to either configure Cassandra to run on port 4000, or change the `cas_native` service locator configuration in `hello-impl/src/main/resources/application.conf`_
 5. Open two more terminals in the `lagom-mvn-standalone`directory
 6. In one terminal:
    1. `cd hello-impl/target/`
    2. `unzip hello-impl-1.0-SNAPSHOT-standalone-bundle.zip` (or, if you don't have the `unzip` command, use any tool to unzip the file into the same directory)
    3. `cd hello-impl-1.0-SNAPSHOT`
    4. `bin/hello-impl` (the equivalent on Windows is left as an exercise for the reader :smile:)
 7. In the other terminal:
    1. `cd stream-impl/target/`
    2. `unzip stream-impl-1.0-SNAPSHOT-standalone-bundle.zip`
    3. `cd stream-impl-1.0-SNAPSHOT`
    4. `bin/stream-impl`

This runs each of the services on the configured port, as single-node clusters.

You can press control-C to exit the services.

## Caveats

This configuration is not suitable for production deployments, it's just intended as a starting point. Here are some of the limitations to be aware of.

* Static service locator configuration is difficult to manage in practice, as it requires configuration to be duplicated across all services, and configuring additional services requires a restart.
* The provided `ConfigurationServiceLocator` only supports a single endpoint per service, which reduces resilience and elasticity.
* These start scripts use `-Dlagom.cluster.join-self=on`, which restricts each service to run on a single node. Creating a multi-node cluster requires configuring seed nodes as described in the [Lagom Cluster documentation](http://www.lagomframework.com/documentation/1.3.x/java/Cluster.html)
* The provided launcher scripts do not attempt to handle process supervision or health monitoring


[Lightbend Production Suite (ConductR)](https://www.lightbend.com/platform/production) provides easy-to-use solutions to these problems and others. 
