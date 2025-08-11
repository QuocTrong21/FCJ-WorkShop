---
title: "Sử dụng AWS SDK for Java để điều khiển AWS"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## 5. Sử dụng AWS SDK for Java

### 4.1. Mục tiêu

- Viết mã Java để tự động điều khiển các tài nguyên AWS như tạo Security Group, thiết lập CloudWatch Alarm, và gửi thông báo SNS.  
- Tăng cường tự động hóa và giám sát hệ thống AWS bằng ứng dụng Java.

---

### 4.2. Cấu trúc Project

- Tạo project Maven sử dụng AWS SDK for Java (phiên bản 2.x).  
- Thêm các dependency cần thiết bao gồm:  
  - EC2 client để quản lý Security Group.  
  - CloudWatch client để tạo và quản lý Alarm.  
  - SNS client để gửi thông báo khi Alarm được kích hoạt.

---

### 4.3. Các bước thực hiện

1. **Setup project và AWS SDK**  
   ### Khởi tạo Maven project. 
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
   - Thêm dependency AWS SDK.  
   - Cấu hình thông tin đăng nhập AWS (AWS credentials).
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

          System.out.print("Nhập VPC ID: ");
          String vpcId = sc.nextLine();

          System.out.print("Nhập EC2 Instance ID để gắn alarm: ");
          String instanceId = sc.nextLine();

          System.out.print("Nhập email nhận cảnh báo SNS: ");
          String email = sc.nextLine();

          try (Ec2Client ec2 = Ec2Client.create();
              SnsClient sns = SnsClient.create();
              CloudWatchClient cloudWatch = CloudWatchClient.create()) {

              String sgId = createSecurityGroup(ec2, vpcId);
              System.out.println("Security Group ID: " + sgId);

              String topicArn = createSnsTopic(sns, email);
              System.out.println("SNS Topic ARN: " + topicArn);
              System.out.println("Vui lòng xác nhận email trước khi alarm hoạt động.");

              createCloudWatchAlarm(cloudWatch, instanceId, topicArn);
              System.out.println("Hoàn tất! Hạ tầng mạng + giám sát đã được cấu hình.");
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
                  .alarmDescription("Alarm khi CPU > 70%")
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
   - Build và chạy Java
   ```bash
      mvn clean package
      java -cp target/aws-networking-1.0.jar com.movie.aws.NetworkAutomationAllInOne
   ```
   - Khi chạy, bạn nhập:
   
    VPC ID (vd: vpc-0a1b2c3d4e5f6g7h)

    EC2 Instance ID để gắn alarm  

    Email nhận cảnh báo → AWS SNS sẽ gửi mail confirm → click link trong mail để xác nhận.
    ![SDK](/images/5.sdk/5.3.png) 
    ![SDK](/images/5.sdk/5.4.png)
    ![SDK](/images/5.sdk/5.5.png)  
 
2. **Tạo Security Group**  
   - Sử dụng SDK để tạo Security Group trong VPC.  
   - Định nghĩa các luật mở cổng (ví dụ SSH, HTTP).
     ![SDK](/images/5.sdk/5.6.png) 

3. **Tạo CloudWatch Alarm**  
   - Định nghĩa Alarm giám sát các chỉ số như CPU Utilization của EC2.  
   - Thiết lập ngưỡng cảnh báo và hành động khi Alarm kích hoạt.
  ![SDK](/images/5.sdk/5.7.png)
  ![SDK](/images/5.sdk/5.8.png)

4. **Gửi thông báo qua SNS khi Alarm kích hoạt**  
   - Tạo và cấu hình SNS topic.  
   - Đăng ký các subscriber (email, SMS, Lambda).  
   - Liên kết SNS topic với Alarm để gửi thông báo tự động.
  ![SDK](/images/5.sdk/5.9.png) 
  ![SDK](/images/5.sdk/5.10.png) 
---

### 4.4. Kết quả

- Java code có thể tự động tạo và quản lý các tài nguyên mạng và giám sát.  
- Tự động gửi cảnh báo khi có sự cố hoặc điều kiện giám sát vượt ngưỡng.  
- Tăng hiệu quả vận hành và giảm sai sót thủ công trong quản lý hạ tầng AWS.

---

Bạn có muốn hỗ trợ thêm về cách tổ chức project hoặc các best practices khi sử dụng AWS SDK for Java không?
