# TDP Ranger Notes

The version 1.2.1-SNAPSHOT of Apache Ranger is based on the `ranger-1.2` tag of the Apache [repository](https://github.com/apache/ranger/tree/ranger-1.2).

## Jenkinfile

The file `./Jenkinsfile-sample` can be used in a Jenkins / Kubernetes environment to build and execute the unit tests of the Hive project. See []() for details on the environment.

## Making a release

```
mvn clean install -DskipTests -Drat.numUnapprovedLicenses=1000
```

This command generates `.tar.gz` files for the Ranger Admin and all the plugins in the `target` directory:

- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-admin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-atlas-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-elasticsearch-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-hbase-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-hdfs-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-hive-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-kafka-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-kms.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-knox-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-kylin-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-migration-util.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-ozone-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-presto-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-ranger-tools.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-solr-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-solr_audit_conf.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-sqoop-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-src.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-storm-plugin.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-tagsync.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-usersync.tar.gz
- ranger-2.0.1-TDP-0.1.0-SNAPSHOT-yarn-plugin.tar.gz

## Testing parameters

```
mvn test -T 4 -DforkCount=4 -Dsurefire.rerunFailingTestsCount=3 --fail-never
```

- -Drat.numUnapprovedLicenses=1000: Workaround for the `tdp` files to be ignored by Apache Rat
- -DforkCount=4: Fork count for the maven-surefire-plugin, defaults to 1
- -Dsurefire.rerunFailingTestsCount: Retries failed test
- --fail-never: Does not interrupt the tests if one module fails

## Build notes

### Ranger 1.2.1-SNAPSHOT with Hadoop 3.1.1-TDP-0.1.0-SNAPSHOT:

[ERROR] Failed to execute goal on project ranger-kms: Could not resolve dependencies for project org.apache.ranger:ranger-kms:jar:1.2.1-SNAPSHOT: Failure to find org.apache.hadoop:hadoop-kms:war:3.1.1-TDP-0.1.0-SNAPSHOT in https://repository.apache.org/content/repositories/snapshots was cached in the local repository, resolution will not be reattempted until the update interval of apache.snapshots.https has elapsed or updates are forced -> [Help 1]

This war file stopped being packaged since Hadoop 3.0.0.

Once `<type>war</type>` at kms/pom.xml#L34 commented, the building fails with:

[ERROR] /tdp/ranger/kms/src/main/java/org/apache/hadoop/crypto/key/kms/server/EagerKeyGeneratorKeyProviderCryptoExtension.java:[63,17] error: org.apache.hadoop.crypto.key.kms.server.EagerKeyGeneratorKeyProviderCryptoExtension.CryptoExtension is not abstract and does not override abstract method reencryptEncryptedKeys(List<EncryptedKeyVersion>) in org.apache.hadoop.crypto.key.KeyProviderCryptoExtension.CryptoExtension
[ERROR] /tdp/ranger/kms/src/main/java/org/apache/hadoop/crypto/key/kms/server/KMSAuthenticationFilter.java:[75,25] error: incompatible types: Text cannot be converted to String
[ERROR] /tdp/ranger/kms/src/main/java/org/apache/hadoop/crypto/key/kms/server/KMSWebApp.java:[171,6] error: cannot find symbol

--> Ranger 1.2 incompatible with Hadoop 3.1.1

### Ranger 2.0:

Seems OK.

### Ranger 2.1:

Fails with:

Caused by: java.io.FileNotFoundException: JAR entry META-INF/MANIFEST.MF not found in /tdp/ranger/security-admin/target/security-admin-web-2.1.1-SNAPSHOT.war

https://issues.apache.org/jira/browse/RANGER-3072

### Ranger 2.2:

Fails with:

[ERROR] /tdp/ranger/ranger-presto-plugin-shim/src/main/java/org/apache/ranger/authorization/presto/authorizer/RangerSystemAccessControlFactory.java:[24] error: cannot find symbol
[ERROR]   symbol:   static throwIfUnchecked
  location: class
/tdp/ranger/ranger-presto-plugin-shim/src/main/java/org/apache/ranger/authorization/presto/authorizer/RangerSystemAccessControlFactory.java:[57,6] error: cannot find symbol

https://issues.apache.org/jira/browse/RANGER-3243

## Test execution notes

See `./test_notes.txt`
