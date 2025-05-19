# APS Programming Assignment

For this assignment we'd like you to implement an overly simplified version of the ClickUp API, specifically the CRUD endpoints related to tasks.



Within APS, ClickUp is the main tasking system that both business and IT heavily use on a daily basis. Among the responsibilities of our team is maintaining the API calls to ClickUp for a wide variety of business processes. For the initiative that is running within APS, we are developing a similar service as to what they are providing but customized to meet our business requirements. The assignment was put together based on this context. Our main goal was to give you something that is relevant to what we are working on while still being feasible and realistic for an assignment.

# Basic information

*   Ideally we'd like to see the code published on GitHub or any other source code management website with a nicely written README.md containing basic information.
*   We do not require you to give an actual database implementation. Storing tasks in-memory is sufficient.
*   At the bottom of this assignment you will find some code snippets you can use to get up and running faster.
*   We do not require you to spend hours working on this assignment to get all requirements working. Only spend as much time on this assignment as you think is right. Needless to say, the more complete the solution, the better we can determine if you are a good fit for us on a technical aspect.
*   What we are looking for in the solution is good structure and architecture. The exact implementation does not need to be of production grade quality, taking shortcuts here and there is perfectly fine. Just be sure to try and finish the requirements while also having fun!

# **Minimum requirements**

*   Make use of the Gradle build tool.
*   Make use of Ktor Server to serve HTTP requests.
*   Implement the API endpoints as described below.
*   Write a couple Unit Tests for (a few) parts that make sense to you. No need for all code to be tested!

# **Ideas for additional 'features'**

Note: there is no need to implement any of these. If you'd like to give your solution some more flare, we recommend you to pick one or two. No need to spend hours working on this assignment!

*   Provide an actual database implementation with Postgres using a library of your choice.
*   Implement (basic) event sourcing for the tasks.
*   Implement a (basic) event driven architecture where a second service can listen to events and log them neatly to the console. Our internal stack makes use of RabbitMQ but you can choose any broker that you're comfortable working with.
*   Implement a (basic) CQRS pattern (can be combined with the 'event driven architecture' feature)
*   Integrate with OpenSearch to index all tasks while keeping them up to date with the latest state (can be combined with the 'event driven architecture' feature)
*   Implement an inbox and / or outbox pattern (can be combined with the 'event driven architecture' feature)
*   Come up with your own creative ideas!

# **Endpoints**

* Only the 'name', 'description', 'due_date' and 'assignees' as input fields need to be supported
* When working with 'assignees', there is no need to have them linked to an actual User object or anything. Just treating them as raw strings is enough.

## POST /api/v2/task

(Subset from: [https://developer.clickup.com/reference/createtask](https://developer.clickup.com/reference/createtask))

```json5
// Request
{
  // Required fields that cannot be omitted, cannot be null and cannot have a blank string.
  "name": "My API Task",

  // Omitting or null is allowed during creation.
  "description": "This is a really nice description",
  
  // Omitting or null is allowed during creation.
  "due_date": "2025-05-15T10:45:33Z",
  
  // Omitting or an empty array is allowed during creation.
  "assignees": ["foo.bar@mail.com"]
}

// Response
{
  // Automatically created and assigned on server
  "id": "c88bb27d-cdd7-4fff-861f-9d84a134cf14",
  "name": "My API Task",  
  "description": "This is a really nice description!",
  "due_date": "2025-05-19T10:00:00Z",
  "assignees": ["foo.bar@mail.com"],

  // Automatically created and assigned on server
  "created_at": "2025-05-14T10:00:00Z"
}
```



## GET /api/v2/task/{uuid}

(Subset from: [https://developer.clickup.com/reference/gettask](https://developer.clickup.com/reference/gettask))

```json5
// Response
{
  "id": "c88bb27d-cdd7-4fff-861f-9d84a134cf14",
  "name": "My API Task",  
  "description": "This is a really nice description!",
  "due_date": "2025-05-19T10:00:00Z",
  "assignees": ["foo.bar@mail.com"],
  "created_at": "2025-05-14T10:00:00Z"
}
```



## PUT /api/v2/task/{uuid}

(Subset from: [https://developer.clickup.com/reference/updatetask](https://developer.clickup.com/reference/updatetask))

```json5
// Request 1
{
  // Can be omitted, cannot be null and cannot be a blank string.
  "name": "My API Task has an updated name",
  
  // Can be omitted, can be null and can be a blank string.
  // It being null or blank means the same thing, it should be set to null.
  "description": null,
  
  // Can be omitted, cannot be null and must be of structure 'add' and 'rem' when value is provided.
  "assignees": {
    "add": ["dummy@mail.com"],
    "rem": ["foo.bar@mail.com"]
  }
}

// Response 1
{
  "id": "c88bb27d-cdd7-4fff-861f-9d84a134cf14",
  "name": "My API Task has an updated name",  
  "description": null,
  "due_date": "2025-05-15T10:00:00Z",
  "assignees": ["dummy@mail.com"],
  "created_at": "2025-05-14T10:00:00Z"
}

////////////////////////////////////////////////////////////

// Request 2
{
  // Can be omitted, can be null and otherwise must be valid timestamp.
  "due_date": "2025-07-04T10:00:00Z"
}

// Response 2
{
  "id": "c88bb27d-cdd7-4fff-861f-9d84a134cf14",
  "name": "My API Task has an updated name",  
  "description": null,
  "due_date": "2025-07-04T10:00:00Z",
  "assignees": ["dummy@mail.com"],
  "created_at": "2025-05-14T10:00:00Z"
}
```



## DELETE /api/v2/task/{uuid}

(Same as [https://developer.clickup.com/reference/deletetask](https://developer.clickup.com/reference/deletetask))

```
No response
```

# **Code snippets**

*   Gradle dependencies (build.gradle.kts):

```kotlin
// Ktor Server dependencies
implementation(group = "io.ktor", name = "ktor-server-core-jvm", version = "3.1.3")
implementation(group = "io.ktor", name = "ktor-server-cio-jvm", version = "3.1.3")
implementation(group = "io.ktor", name = "ktor-server-content-negotiation", version = "3.1.3")
implementation(group = "io.ktor", name = "ktor-serialization-jackson", version = "3.1.3")

// Jackson dependencies
implementation(group = "com.fasterxml.jackson.core", name = "jackson-databind", version = "2.18.3")
implementation(group = "com.fasterxml.jackson.datatype", name = "jackson-datatype-jsr310", version = "2.18.3")
implementation(group = "com.fasterxml.jackson.module", name = "jackson-module-kotlin", version = "2.18.3")

// Mocking dependencies
testImplementation(group = "io.mockk", name = "mockk", version = "1.14.2")
testImplementation(group = "io.mockk", name = "mockk-agent", version = "1.14.2")

// Dependencies for the extra features
// RabbitMQ dependencies
implementation(group = "com.rabbitmq", name = "amqp-client", version = "5.25.0")

// OpenSearch dependencies
implementation(group = "org.opensearch.client", name = "opensearch-java", version = "2.22.0")
implementation(group = "org.apache.httpcomponents.client5", name = "httpclient5", version = "5.2.1")
```



*   Docker Compose (docker-compose.yml):

```yaml
services:
  rabbitmq:
    image: rabbitmq:4.0.2-management-alpine
    ports:
      - '5672:5672'
      - '15672:15672'

  postgres:
    image: postgres:17.2-alpine
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    ports:
      - '5432:5432'

  opensearch-node:
    image: opensearchproject/opensearch:latest
    container_name: opensearch-node
    environment:
      - cluster.name=opensearch-cluster
      - node.name=opensearch-node
      - bootstrap.memory_lock=true
      - "OPENSEARCH_JAVA_OPTS=-Xms1024m -Xmx1024m"
      - "DISABLE_INSTALL_DEMO_CONFIG=true"
      - "DISABLE_SECURITY_PLUGIN=true"
      - "discovery.type=single-node"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    ports:
      - "9200:9200"
      - "9600:9600"

  opensearch-dashboards:
    image: opensearchproject/opensearch-dashboards:latest
    container_name: opensearch-dashboards
    ports:
      - "5601:5601"
    expose:
      - "5601"
    environment:
      - 'OPENSEARCH_HOSTS=["http://opensearch-node:9200"]'
      - "DISABLE_SECURITY_DASHBOARDS_PLUGIN=true"
```