Pact framework for contract testing between a consumer and a provider.

In this example, we'll create a simple REST API for managing users. The consumer will make a GET request to the provider's API to retrieve a user by ID.

First, let's define the expected response using JSON Schema Draft 2012 in the consumer project:

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "User",
  "type": "object",
  "properties": {
    "id": {
      "type": "integer"
    },
    "name": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    }
  },
  "required": ["id", "name", "email"]
}
```

Next, let's create a Pact test in the consumer project:

```java
import au.com.dius.pact.consumer.dsl.DslPart;
import au.com.dius.pact.consumer.dsl.PactDslJsonBody;
import au.com.dius.pact.consumer.dsl.PactDslWithProvider;
import au.com.dius.pact.consumer.junit5.PactConsumerTestExt;
import au.com.dius.pact.consumer.junit5.PactVerificationContext;
import au.com.dius.pact.consumer.junit5.PactVerificationInvocationContext;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

import static au.com.dius.pact.consumer.dsl.LambdaDsl.newJsonBody;
import static org.junit.jupiter.api.Assertions.assertEquals;

@ExtendWith(PactConsumerTestExt.class)
public class ContractTestingExample {

  @Test
  @PactTestFor(providerName = "your-provider-name", port = "your-provider-port")
  public void testContract(PactDslWithProvider builder) {
    DslPart jsonSchemaResponse = newJsonBody((json) -> {
      json.stringType("id");
      json.stringType("name");
      json.stringType("email").format("email");
    });

    builder
      .given("a user exists")
      .uponReceiving("a request for a user")
      .path("/users/{id}")
      .withUrlPathParam("id", "123")
      .willRespondWith()
      .status(200)
      .headers(header -> header.contentType("application/json"))
      .body(jsonSchemaResponse);
  }

  @Test
  public void testConsumer(PactVerificationContext context, PactVerificationInvocationContext invocationContext) {
    context.verifyInteraction();
  }
}
```

In this example, the `jsonSchemaResponse` variable defines the JSON Schema for the expected response using JSON Schema Draft 2012.

After running the Pact test in the consumer project, a Pact file will be generated that describes the expected interactions between the consumer and the provider.

Finally, let's validate the interactions against the generated Pact file in the provider project:

```java
import au.com.dius.pact.provider.junit5.PactVerification;
import au.com.dius.pact.provider.junit5.PactVerificationContext;
import au.com.dius.pact.provider.junit5.PactVerificationInvocationContext;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

@ExtendWith(PactProviderTestExt.class)
public class ContractTestingExample {

  @Test
  @PactVerification(fragment = "your-pact-fragment")
  public void testProvider(PactVerificationContext context, PactVerificationInvocationContext invocationContext) {
    context.verifyInteraction();
  }
}
```

In this example, the `@PactVerification` annotation specifies the Pact fragment to use for the verification.

By following these steps, you can effectively perform contract testing on a REST API using the Pact framework and JSON Schema Draft 2012.
