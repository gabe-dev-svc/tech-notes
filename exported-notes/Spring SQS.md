#  Spring SQS

## Receiving Messages

- Only `String` payloads allowed. 
- Payloads with a MIME type of `application/json` get serialized into a POJO
- No transasction support, messges might be received twice. **Check for to make sure messages aren't duplicated.**
- `QueueMessagingTemplate` can be used to send messages conveniently. 
  - Send methods can also specify destination using `QueueMessageChannel` object, others use a String. 
  - No desintion argument uses default destination. 

_Example Message Sender_
```Java
import com.amazonaws.services.sqs.AmazonSQS;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.aws.messaging.core.QueueMessagingTemplate;
import org.springframework.messaging.support.MessageBuilder;

public class SqsQueueSender {

	private final QueueMessagingTemplate queueMessagingTemplate;

	@Autowired
	public SqsQueueSender(AmazonSQS amazonSqs) {
		this.queueMessagingTemplate = new QueueMessagingTemplate(amazonSqs);
	}

	public void send(String message) {
		this.queueMessagingTemplate.send("physicalQueueName", MessageBuilder.withPayload(message).build());
	}
}
```
Notice how `MessageBuilder` is used to create a message with a payload. The string value must match a queue name defined on AWS.

>Optionally a `ResourceIdResolver`  implementation can be passed to the `QueueMessagingTemplate` constructor to resolve resources by logical name when running inside a CloudFormation stack.

- `MessageConverter` interface has methods `convertAndSend()` and `receiveAndConver()` 
  - `SimpleMessageConverter` unwraps message payloads as long as it matches target type.

## Sending Messages

- Methods annotated with `MessageMapping` will route the messages to the annotated methods.

```JAVA
@SqsListener("queueName")
public void queueListener(Person person) {
	// ...
}
```

- Using the `@SendTo` annotation requires you place the annotation below the listener annotation `@SqsListener`.
- Modifying the way that messages are returned can be achieved by configuring the `QueueMessageHandlerFactory` and providing it a `QueueMessagingTemplate` or an extension thereof. 
```JAVA
//SqsConfig.class
  @Bean
  public QueueMessageHandlerFactory queueMessageHandlerFactory(AmazonSQSAsync amazonSQSAsync) {
    QueueMessageHandlerFactory factory = new QueueMessageHandlerFactory();
    factory.setAmazonSqs(amazonSQSAsync);
    factory.setSendToMessagingTemplate(queueMessagingTemplate(amazonSQSAsync));
    return factory;
  }

  @Bean
  public CubeServiceMessagingTemplate queueMessagingTemplate(AmazonSQSAsync sqsAsync) {
    CubeServiceMessagingTemplate queueMessagingTemplate = new CubeServiceMessagingTemplate(
        sqsAsync);
    return queueMessagingTemplate;
  }

//CubeServiceMessagingTemplate.java
  public class CubeServiceMessagingTemplate extends QueueMessagingTemplate {

  public CubeServiceMessagingTemplate(AmazonSQSAsync amazonSqs) {
    super(amazonSqs);
  }

  @Override
  public <T> void convertAndSend(String destinationName, T payload) {
    QueueMessageChannel channel = resolveMessageChannel(destinationName);
    Map<String, Object> queueHeaders = new HashMap<String, Object>() {{
      put(SqsMessageHeaders.SQS_GROUP_ID_HEADER, "service-ilc-cube");
    }};
    convertAndSend(channel, payload, queueHeaders);
  }
}
```

- To serialize outbound objects simply create a `QueueMessageHandlerFactory` bean and set the `setSendToMessagingTemplate()` to a messaging template -- it can be a `new QueueMessagingTemplate()`

