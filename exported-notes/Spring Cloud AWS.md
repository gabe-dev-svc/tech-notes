# Spring Cloud AWS

## SQS

## Spring JDBC + AWS

### Required Dependency

Maven

```xml
<dependency>
<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-aws-jdbc</artifactId>
	<version>{spring-cloud-version}</version>
</dependency>
```

Gradle

```yaml
implementation 'org.springframework.cloud:spring-cloud-aws-jdbc:{spring-cloud-version}
```

### Configuration

- Data source configuration requires security and region configuration as a minimum
- Requires a valid `db-instance-identifier` that points to valid Amazon RDS database instance
- Also requires master user and master password
- Data source can also be configured with Spring Boot
  - General property name sets data source identifier: `cloud.aws.rds.<instanceName>` where `instanceName` is the name of the concrete instance

|       property     | example | description |
| ------------------ | ------- | ----------- |
| cloud.aws.rds.test |         | `test` is the name of the database instance |
| cloud.aws.rds.test.password | password | DB instance password |
| cloud.aws.rds.test.username | username | DB instance username |
| cloud.aws.rds.test.readReplicaSupport | true | If read-replicas should be used for the data source |
| cloud.aws.rds.test.databaseName | fooDb | Custom database name if the default one from rds should not be used |

### CloudFormation Support

- Databases configured with CloudFormation are also supported. 

