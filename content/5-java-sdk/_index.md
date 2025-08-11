---
title: "Using AWS SDK for Java to Control AWS"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

##  Using AWS SDK for Java

### Objectives

- Write Java code to automatically control AWS resources such as creating Security Groups, setting up CloudWatch Alarms, and sending SNS notifications.  
- Enhance automation and monitoring of AWS systems using Java applications.

---

### Project Structure

- Create a Maven project using AWS SDK for Java (version 2.x).  
- Add necessary dependencies including:  
  - EC2 client for managing Security Groups.  
  - CloudWatch client for creating and managing Alarms.  
  - SNS client for sending notifications when Alarms are triggered.

---

### Implementation Steps

1. **Setup project and AWS SDK**  
   ### Initialize Maven project. 
   ```bash
      <project xmlns="http://maven.apache.org/POM/4.0.0" 
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                                  http://maven.apache.org/maven-v4_0_0.xsd">
          <modelVersion>4.0.0</modelVersion>
          <groupId>com.movie.aws</groupId>
          <artifactId>aws-networking</artifactId>
          <version>1.0</version>

          <dependencyManagement>
              <dependencies>
                  <dependency>
                      <groupId>software.amazon.awssdk</groupId>
                      <artifactId>bom</artifactId>
                      <version>2.25.24</version>
                      <type>pom</type>
                      <scope>import</scope>
                  </dependency>
              </dependencies>
          </dependencyManagement>

          <dependencies>
              <dependency>
                  <groupId>software.amazon.awssdk</groupId>
                  <artifactId>ec2</artifactId>
              </dependency>
              <dependency>
                  <groupId>software.amazon.awssdk</groupId>
                  <artifactId>cloudwatch</artifactId>
              </dependency>
              <dependency>
                  <groupId>software.amazon.awssdk</groupId>
                  <artifactId>sns</artifactId>
              </dependency>
          </dependencies>
          <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <version>3.8.1</version>
                  <configuration>
                      <source>17</source>
                      <target>17</target>
                  </configuration>
              </plugin>
          </plugins>
          </build>
      </project>

     ``` 
   ![SDK](/images/5.sdk/5.1.png) 
   ![SDK](/images/5.sdk/5.2.png) 
   - Add AWS SDK dependency.  
   - Configure AWS credentials.
   ```bash
      package com.movie.aws;
      import software.amazon.awssdk.services.ec2.Ec2Client;
      import software.amazon.awssdk.services.ec2.model.*;
      import software.amazon.awssdk.services.sns.SnsClient;
      import software.amazon.awssdk.services.sns.model.*;
      import software.amazon.awssdk.services.cloudwatch.CloudWatchClient;
      import software.amazon.awssdk.services.cloudwatch.model.*;
      import java.util.Scanner;

    public class NetworkAutomationAllInOne {

      public static void main(String[] args) {
          Scanner sc = new Scanner(System.in);

          System.out.print("Enter VPC ID: ");
          String vpcId = sc.nextLine();

          System.out.print("Enter EC2 Instance ID to attach alarm: ");
          String instanceId = sc.nextLine();

          System.out.print("Enter email for SNS alerts: ");
          String email = sc.nextLine();

          try (Ec2Client ec2 = Ec2Client.create();
              SnsClient sns = SnsClient.create();
              CloudWatchClient cloudWatch = CloudWatchClient.create()) {

              String sgId = createSecurityGroup(ec2, vpcId);
              System.out.println("Security Group ID: " + sgId);

              String topicArn = createSnsTopic(sns, email);
              System.out.println("SNS Topic ARN: " + topicArn);
              System.out.println("Please confirm email before alarm becomes active.");

              createCloudWatchAlarm(cloudWatch, instanceId, topicArn);
              System.out.println("Complete! Network infrastructure + monitoring has been configured.");
          }
      }

      private static String createSecurityGroup(Ec2Client ec2, String vpcId) {
          CreateSecurityGroupResponse sgResponse = ec2.createSecurityGroup(
                  CreateSecurityGroupRequest.builder()
                          .groupName("MovieWebSG")
                          .description("SG for Movie Web Java App")
                          .vpcId(vpcId)
                          .build()
          );
          String sgId = sgResponse.groupId();

          ec2.authorizeSecurityGroupIngress(AuthorizeSecurityGroupIngressRequest.builder()
                  .groupId(sgId)
                  .ipPermissions(
                          IpPermission.builder().ipProtocol("tcp").fromPort(22).toPort(22)
                                  .ipRanges(IpRange.builder().cidrIp("0.0.0.0/0").build()).build(),
                          IpPermission.builder().ipProtocol("tcp").fromPort(80).toPort(80)
                                  .ipRanges(IpRange.builder().cidrIp("0.0.0.0/0").build()).build(),
                          IpPermission.builder().ipProtocol("tcp").fromPort(443).toPort(443)
                                  .ipRanges(IpRange.builder().cidrIp("0.0.0.0/0").build()).build(),
                          IpPermission.builder().ipProtocol("tcp").fromPort(3306).toPort(3306)
                                  .ipRanges(IpRange.builder().cidrIp("10.0.0.0/16").build()).build()
                  )
                  .build());

          return sgId;
      }

      private static String createSnsTopic(SnsClient sns, String email) {
          CreateTopicResponse topicResponse = sns.createTopic(
                  CreateTopicRequest.builder().name("HighCPUAlert").build()
          );
          String topicArn = topicResponse.topicArn();

          sns.subscribe(SubscribeRequest.builder()
                  .protocol("email")
                  .endpoint(email)
                  .topicArn(topicArn)
                  .build());

          return topicArn;
      }

      private static void createCloudWatchAlarm(CloudWatchClient cloudWatch, String instanceId, String topicArn) {
          PutMetricAlarmRequest alarmRequest = PutMetricAlarmRequest.builder()
                  .alarmName("HighCPU")
                  .comparisonOperator(ComparisonOperator.GREATER_THAN_THRESHOLD)
                  .evaluationPeriods(1)
                  .metricName("CPUUtilization")
                  .namespace("AWS/EC2")
                  .period(300)
                  .statistic(Statistic.AVERAGE)
                  .threshold(70.0)
                  .alarmDescription("Alarm when CPU > 70%")
                  .dimensions(Dimension.builder()
                          .name("InstanceId")
                          .value(instanceId)
                          .build())
                  .actionsEnabled(true)
                  .alarmActions(topicArn)
                  .unit(StandardUnit.PERCENT)
                  .build();

          cloudWatch.putMetricAlarm(alarmRequest);
      }
    }
   ```
   - Build and run Java
   ```bash
      mvn clean package
      java -cp target/aws-networking-1.0.jar com.movie.aws.NetworkAutomationAllInOne
   ```
   - When running, you enter:

    VPC ID (e.g.: vpc-0a1b2c3d4e5f6g7h)

    EC2 Instance ID to attach alarm  

    Email for alerts → AWS SNS will send confirmation email → click link in email to confirm.
    ![SDK](/images/5.sdk/5.3.png) 
    ![SDK](/images/5.sdk/5.4.png)
    ![SDK](/images/5.sdk/5.5.png)  
 
2. **Create Security Group**  
   - Use SDK to create Security Group in VPC.  
   - Define rules for opening ports (e.g., SSH, HTTP).
     ![SDK](/images/5.sdk/5.6.png) 

3. **Create CloudWatch Alarm**  
   - Define Alarm to monitor metrics like EC2 CPU Utilization.  
   - Set alert thresholds and actions when Alarm triggers.
  ![SDK](/images/5.sdk/5.7.png)
  ![SDK](/images/5.sdk/5.8.png)

4. **Send notifications via SNS when Alarm triggers**  
   - Create and configure SNS topic.  
   - Register subscribers (email, SMS, Lambda).  
   - Link SNS topic with Alarm to send automatic notifications.
  ![SDK](/images/5.sdk/5.9.png) 
  ![SDK](/images/5.sdk/5.10.png) 
---

### Results

- Java code can automatically create and manage network resources and monitoring.  
- Automatically send alerts when incidents occur or monitoring conditions exceed thresholds.  
- Increase operational efficiency and reduce manual errors in AWS infrastructure management.

---