# TDP Ranger Notes

The version `3.0.0-TDP-0.1.0-SNAPSHOT` of Apache Ranger is based on the `master` branch of the Apache [repository](https://github.com/apache/ranger/tree/master).

**Note:** The only purpose of this branch is to build the Ranger-Kafka plugin with compatibility with the new `org.apache.kafka.server.authorizer` package.

## Making a release

You will need `Maven>=3.6.3`. You can download `apache-maven-3.6.3-bin.tar.gz` from [Apache Archive](https://archive.apache.org/dist/maven/maven-3/3.6.3/binaries/) and extract the tar.gz in your `TDP_HOME_DIR`.

To build the Ranger-Kafka plugin and dependencies:

```
../apache-maven-3.6.3/bin/mvn clean package compile install -DskipTests -Drat.numUnapprovedLicenses=1000 -P '!all,!linux,ranger-kafka-plugin'
```

This command generates `ranger-3.0.0-TDP-0.1.0-SNAPSHOT-kafka-plugin.tar.gz` in the `target` directory.

## Testing parameters

```
../apache-maven-3.6.3/bin/mvn test -T 4 -DforkCount=4 -Dsurefire.rerunFailingTestsCount=3 --fail-never -P '!all,!linux,ranger-kafka-plugin'
```

- -Drat.numUnapprovedLicenses=1000: Workaround for the `tdp` files to be ignored by Apache Rat
- -DforkCount=4: Fork count for the maven-surefire-plugin, defaults to 1
- -Dsurefire.rerunFailingTestsCount: Retries failed test
- --fail-never: Does not interrupt the tests if one module fails
