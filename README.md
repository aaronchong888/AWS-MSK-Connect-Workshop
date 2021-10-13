# AWS-MSK-Connect-Workshop

Welcome to the Amazon MSK Connect Workshop. [Amazon MSK Connect](https://aws.amazon.com/msk/features/msk-connect/) is a feature of [Amazon MSK (Managed Streaming for Apache Kafka)](https://aws.amazon.com/msk/), which enables you to run fully managed Apache Kafka Connect workloads on AWS. Today you will learn about the Amazon MSK Connect feature and how to easily deploy, monitor, and automatically scale connectors that move data between Apache Kafka clusters and external systems.

The workshop is divided into multiple modules. Each module targets a specific connector.

### Costs
Some of the labs in the workshop will require you to create resources in your AWS cloud account. This will incur costs that may not be covered in AWS free tier. We recommend that you clean up the resources once you are finished with the workshop to stop incurring any additional charges. 

You may refer to the [Clean Up](https://github.com/aaronchong888/AWS-MSK-Connect-Workshop#clean-up) section for the instructions to clean up the resources created within the workshop.

### Regions
The workshop is supported in the regions where the Amazon MSK Connect feature is available. Workshop has been tested in following regions.

- **US East (N. Virginia)** (us-east-1)
- **Asia Pacific (Singapore)** (ap-southeast-1)

> At the time of writing (13 Oct 2021), MSK Connect is available in the following [AWS Regions](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/#Regions): Asia Pacific (Mumbai), Asia Pacific (Seoul), Asia Pacific (Singapore), Asia Pacific (Sydney), Asia Pacific (Tokyo), Canada (Central), EU (Frankfurt), EU (Ireland), EU (London), EU (Paris), EU (Stockholm), South America (Sao Paulo), US East (N. Virginia), US East (Ohio), US West (N. California), US West (Oregon). For the lastest information, visit the [AWS Regional Services List](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).

## Lab Setup

#### Create an EC2 SSH Key Pair for logging into the EC2 instance we will use in the workshop

1. Open the Amazon EC2 console at [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)

Choose a region where the Amazon MSK Connect feature is available (e.g. **us-east-1**), this will be the region that you are using for the workshop.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-1.png" width="90%"></p>
<br>

2. Choose **Key Pairs** in the navigation bar on the left, and click on **Create key pair**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-2.png" width="90%"></p>
<br>

3. Provide a name for the key pair (e.g. **mskconnect-workshop-keypair**), and click on **Create key pair**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-3.png" width="90%"></p>
<br>

4. You should see the message **Successfully created key pair**, and confirm the download of the generated **.pem** file to your local machine.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-4.png" width="90%"></p>
<br>

#### Deploy required AWS resources via CloudFormation

5. Download the CloudFormation template provided in the repo: [msk-connect-workshop.yml](https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/CloudFormation%20Template/msk-connect-workshop.yml)

6. Open the AWS CloudFormation console at [https://console.aws.amazon.com/cloudformation/](https://console.aws.amazon.com/cloudformation/)

7. Click on **Create stack** and select **With new resources (standard)**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-7.png" width="90%"></p>
<br>

8. Choose **Upload a template file** option and upload the CloudFormation template from your local machine, then click **Next**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-8.png" width="90%"></p>
<br>

9. Enter **mskconnect-workshop** as the stack name, choose the **mskconnect-workshop-keypair** under **KeyName**, and then click **Next**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-9.png" width="90%"></p>
<br>

10. Leave the default settings on the step **Configure stack options** and click **Next**.

11. Scroll down to the bottom of the **Review mskconnect-workshop** screen and check the box to acknowledge the creation of IAM resources. Click **Create stack** to trigger the creation of the CloudFormation stack.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-11.png" width="90%"></p>
<br>

12. Wait until the stack creation is completed before proceeding to the next steps. It may take a while for the MSK cluster creation (~30 to 45mins).

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step0-12.png" width="90%"></p>
<br>

## Source Connectors

### Salesforce

#### Create custom plugin

Download the Salesforce Connector from Confluent: 
[https://www.confluent.io/hub/confluentinc/kafka-connect-salesforce](https://www.confluent.io/hub/confluentinc/kafka-connect-salesforce)

> Please note that this connector is a Confluent Commercial Connector and supported by Confluent. The requires purchase of a Confluent Platform subscription, including a license to this Commercial Connector. You can also use this connector for a 30-day trial without an enterprise license key - after 30 days, you need to purchase a subscription.

1. Click and download the ZIP file:

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step1-1.png" width="90%"></p>
<br>

2. Upload the ZIP file to the S3 bucket created by CloudFormation (e.g. mskconnect-bucket-**AccountId**-**Region**-**RandomGUID**).

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step1-2.png" width="90%"></p>
<br>

3. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)

In the left pane expand **MSK Connect**, then choose **Custom plugins**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step1-3.png" width="90%"></p>
<br>

4. Choose **Create custom plugin**, and choose the ZIP file uploaded in Step 2.

5. Enter **salesforce-connector-plugin** for the custom plugin name, then choose **Create custom plugin**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step1-5.png" width="90%"></p>
<br>

6. Wait for a few minutes for the creation process to complete. You should see the following message in a banner at the top of the browser window when completed.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step1-6.png" width="90%"></p>
<br>

#### Create Apache Kakfa topic

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)

Choose the MSK Cluster created by CloudFormation (e.g. MSKCluster-**RandomGUID**), then click on **View client information**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step2-1.png" width="90%"></p>
<br>

2. Copy the **Bootstrap servers** and **Apache ZooKeeper connection** strings, you will need to use them in the later steps.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step2-2.png" width="90%"></p>
<br>

3. Open the Amazon EC2 console at [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)

Find the EC2 instance named **KafkaClientInstance**, right-click on it and choose **Connect**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step2-3.png" width="90%"></p>
<br>

4. Go to the **SSH client** tab and follow the instructions to ssh into the EC2 instance.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step2-4.png" width="90%"></p>
<br>

5. Run the following command on the EC2 instance, replacing **ZookeeperConnectString** with the value that you saved when you viewed the cluster's client information.

```
kafka/kafka_2.12-2.2.1/bin/kafka-topics.sh --create --zookeeper <ZookeeperConnectString> --replication-factor 2 --partitions 1 --topic mskconnect-salesforce-topic
```

If the command succeeds, you should see the message: **Created topic mskconnect-salesforce-topic.**

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step2-5.png" width="90%"></p>
<br>

#### Salesforce Setup

1. Go to [https://developer.Salesforce.com/signup](https://developer.Salesforce.com/signup) and register for a trial of the Developer Edition account in Salesforce.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step3-1.png" width="90%"></p>
<br>

2. Create a new **Connected App** in Salesforce. Go to **Setup**, search for **App Manager** in the search box. Click the **App Manager** under **Apps**, and then click the **New Connected App** button to set up a new API client.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step3-2.png" width="90%"></p>
<br>

3. Enable the OAuth settings. For testing purpose, provide full API access:

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step3-3.png" width="90%"></p>
<br>

4. Copy the Consumer Key and Consumer Secret, you will need to use them in the later steps.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step3-4.png" width="90%"></p>
<br>

5. Click on the Profile icon and go to the **Settings**. Choose **Reset My Security Token** to generate a new security token, it will be sent to your email address.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step3-5.png" width="90%"></p>
<br>

#### Create connector

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)

In the left pane expand **MSK Connect**, then choose **Connectors**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-1.png" width="90%"></p>
<br>

2. Click **Create connector**, and choose the **salesforce-connector-plugin**, and then click **Next**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-2.png" width="90%"></p>
<br>

3. For the Connector name, enter **mskconnect-salesforce-connector**. Choose the **MSK cluster** type and select the MSK Cluster created by CloudFormation (e.g. MSKCluster-**RandomGUID**).

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-3.png" width="90%"></p>
<br>

4. For the Connector configuration, paste the following values and update the corresponding fields with `<--PLACEHOLDER-->` (e.g. **salesforce.username=abc@test.com**):
```
connector.class=io.confluent.salesforce.SalesforcePushTopicSourceConnector
salesforce.username=<--YOUR-SALESFORCE-USERNAME-->
confluent.topic.bootstrap.servers=<--YOUR-MSK-CLUSTER-BOOTSTRAP-SERVERS-->
tasks.max=1
salesforce.consumer.key=<--YOUR-SALESFORCE-CONSUMER-KEY-->
salesforce.push.topic.name=LeadsPushTopic
salesforce.password=<--YOUR-SALESFORCE-PASSWORD-->
salesforce.password.token=<--YOUR-SALESFORCE-SECURITY-TOKEN-->
salesforce.initial.start=all
confluent.topic.replication.factor=1
kafka.topic=mskconnect-salesforce-topic
salesforce.consumer.secret=<--YOUR-SALESFORCE-CONSUMER-SECRET-->
salesforce.object=Lead
```

For details on the configuration properties, refer to the [Salesforce Connector documentation](https://docs.confluent.io/kafka-connect-salesforce/current/overview.html).

> *salesforce.initial.start*
>
> Specify the initial starting point for the connector for replaying events. Use **all** to send a replayId of -2 to Salesforce that replays all events from last 24 hours, or use **latest** to send a replayId of -1 to Salesforce that plays only new incoming events that arrive after the connector has started. 
>
>The default is **latest** in case there are more enqueued events than might be allowed by API limits.
<br>

5. For the Access permissions, choose the IAM role created by CloudFormation (e.g. **StackName**-MSKConnectRole-**RandomGUID**), and then click **Next**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-5.png" width="90%"></p>
<br>

6. Keep the default settings on Security, then click **Next**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-6.png" width="90%"></p>
<br>

7. For Logs, choose **Deliver to Amazon CloudWatch Logs** and **Browse** to search for the log group created for MSK Connect by CloudFormation (e.g. **StackName**-MSKConnectLogGroup-**RandomGUID**), and then click **Next**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-7.png" width="90%"></p>
<br>

8. Review and choose **Create connector**. Wait for a few minutes until the connector is successfully created.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-8.png" width="90%"></p>
<br>

9. You may scroll down to the bottom and find the logs of the connector in CloudWatch Logs. This will be very helpful for you to troubleshoot the MSK Connect connectors.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step4-9.png" width="90%"></p>
<br>

#### Test 

1. Login to your Salesforce Developer account and search for **Leads** in the search box. Click on **New** to create a new Lead in Salesforce to generate some test data.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step5-1.png" width="90%"></p>
<br>

2. In the previous SSH session of the EC2 Client, run the following command to subscribe to the topic **mskconnect-salesforce-topic**, replacing **BootstrapBrokerString** with the value that you saved when you viewed the cluster's client information.

```
kafka/kafka_2.12-2.2.1/bin/kafka-console-consumer.sh --bootstrap-server <BootstrapBrokerString> --topic mskconnect-salesforce-topic --from-beginning
```

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/step5-2.png" width="90%"></p>
<br>

## Sink Connectors

### Amazon S3

[Follow the steps on the MSK Conect Doc](https://docs.aws.amazon.com/msk/latest/developerguide/mkc-create-plugin.html)

## Clean Up

Follow the instructions below to clean up the workshop environment.

1. First we will need to clean up the created S3 bucket. Note that this step will delete all the data stored in the S3 bucket. Open the Amazon S3 console at [https://console.aws.amazon.com/s3/](https://console.aws.amazon.com/s3/)

Search for **mskconnect** in the search box, select the target bucket (e.g. mskconnect-bucket-**AccountId**-**Region**-**RandomGUID**) and click the **Empty** button on the top right.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-1.png" width="90%"></p>
<br>

2. On the **Empty Bucket** screen, type **permanently delete** to confirm deletion and click **Empty**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-2.png" width="90%"></p>
<br>

3. You should see a confirmation that the S3 bucket is successfully emptied.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-3.png" width="90%"></p>
<br>

4. Next we will delete the MSK Connect connectors that we created previously. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)

In the left pane choose **Connectors** under **MSK Connect**, select the target Connector to be deleted (e.g. **mskconnect-salesforce-connector**) and click the **Delete** button on the top right.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-4.png" width="90%"></p>
<br>

5. Confirm deletion by typing in the connector name (e.g. **mskconnect-salesforce-connector**) and click **Delete**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-5.png" width="90%"></p>
<br>

6. You should see a confirmation that the connector is successfully deleted. Repeat the steps to delete all the connectors.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-6.png" width="90%"></p>
<br>

7. Finally we will delete the CloudFormation stack to clean up all the related resources created in the workshop. Open the AWS CloudFormation console at [https://console.aws.amazon.com/cloudformation/](https://console.aws.amazon.com/cloudformation/)

Select the CloudFormation stack that you have created during the Lab Setup section (e.g. **mskconnect-workshop**), click the **Delete** button to the top right.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-7.png" width="90%"></p>
<br>

8. Confirm deletion by clicking **Delete stack**.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-8.png" width="90%"></p>
<br>

9. You should see that the stack deletion is initiated now. Once the stack is deleted, it will disappear from the stack list. You may refresh the page by clicking the refresh button.

<p align="center"><img alt="" src="https://github.com/aaronchong888/AWS-MSK-Connect-Workshop/blob/main/img/cleanup-9.png" width="90%"></p>
<br>

**The above steps should clean up all the resources that we have previously provisioned in the MSK Connect Workshop.**

## Authors

* **Aaron Chong** - [aaronchong888](https://github.com/aaronchong888)

## Acknowledgments

This workshop is built referencing the AWS Developer Guides, AWS Blog, and other AWS workshop contents listed below:
- [Amazon MSK Developer Guide](https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html)
- [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_MSK.html)
- [Introducing Amazon MSK Connect – Stream Data to and from Your Apache Kafka Clusters Using Managed Connectors](https://aws.amazon.com/blogs/aws/introducing-amazon-msk-connect-stream-data-to-and-from-your-apache-kafka-clusters-using-managed-connectors/)
- [Amazon AppFlow Workshop](https://appflow-immersionday.workshop.aws/en/)
- [Amazon MSK Labs](https://amazonmsk-labs.workshop.aws/en/)
