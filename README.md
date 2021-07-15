## code derived from AWS IOT SDK for Java

## AWS IOT JAVA SDK - With out any errors 
## will mention the steps to skip the errors

## On Board the device in AWS IOT CORE

While onboarding the device you get option to download the following certificates(windows onboarding with JAVA SDK )

* ThingName.cert.pem
* ThingName.private
* ThingName.public
* start.ps1

Initially we have default policy added while onboarding the IOT device that needed to customize later to avoid errors

After the above files are download place them in separate folder

Now run the start.ps1 using the powershell that will download **root-CA** certificate for you and will also download the defualt **AWS IOT SDK for Java** and it will try to run the code with get the below error as 

[WARNING]
java.lang.ClassNotFoundException: com.amazonaws.services.iot.client.sample.pubSub.PublishSubscribeSample
    at java.net.URLClassLoader.findClass (URLClassLoader.java:471)
    at java.lang.ClassLoader.loadClass (ClassLoader.java:588)
    at java.lang.ClassLoader.loadClass (ClassLoader.java:521)
    at org.codehaus.mojo.exec.ExecJavaMojo$1.run (ExecJavaMojo.java:246)
    at java.lang.Thread.run (Thread.java:834)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.040 s
[INFO] Finished at: 2021-07-14T18:34:54-06:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.codehaus.mojo:exec-maven-plugin:3.0.0:java (default-cli) on project aws-iot-device-sdk-java-samples: An exception occured while executing the Java class. com.amazonaws.services.iot.client.sample.pubSub.PublishSubscribeSample -> [Help 1]


**In the above case go to package ## com.amazonaws.services.iot.client.util and class AwsIotWebSocketUrlSigner.jaa and remove comments containing @Deprecated**

Now run **mvn clean install -Dgpg.skip=true** in side SDK project now the build with be success

Now try to run **start.ps1** it will start publishing the data into MQTT TOPIC

You can subscribe to the data published Iin **AWS IOT CORE -> Test -> MQTT Subscribe** in subcribe enter # default value to get data. 

In the above **start.ps1** we are running **PublishSubscribeSample.java** sample 

If you want to run **ShadowSample.java** which will publish a random sample temperature data

Initial you need to change the IOT Device policy of the thing which is created to below

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:*"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}

The default created policy wont allow you to run shadow on the Things

After updating the policy as above run the below command in sdk folder

mvn exec:java -pl aws-iot-device-sdk-java-samples -Dexec.mainClass="com.amazonaws.services.iot.client.sample.shadow.ShadowSample" -Dexec.args="-clientEndpoint a2u5sgepxcs51y-ats.iot.us-east-2.amazonaws.com -clientId sdk-java -thingName IOTCoreDevice -certificateFile ..\IOTCoreDevice.cert.pem -privateKeyFile ..\IOTCoreDevice.private.key"


Replace IOTCoreDevice name with your respective Thing Name.
