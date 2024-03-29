         
> The main idea of creating this project is implementing an infrastructure for up and running distributed system with the latest technology and architecture like Vertical Slice Architecture, Event Driven Architecture, CQRS, Postgres, RabbitMq and Nestjs, and we will not deal mainly with business. 🚀

## Technologies - Libraries
- [`microsoft/TypeScript`](https://github.com/microsoft/TypeScript) - TypeScript is a language for application-scale JavaScript.
- [`nestjs/nest`](https://github.com/nestjs/nest) - Nest is a framework for building efficient, scalable Node.js server-side applications
- [`nestjs/cqrs`](https://github.com/nestjs/cqrs) - A lightweight CQRS module for Nest framework (node.js)
- [`nestjs/typeorm`](https://github.com/nestjs/typeorm) - TypeORM module for Nest
- [`nestjs/swagger`](https://github.com/nestjs/swagger) - OpenAPI (Swagger) module for Nest
- [`nestjs/passport`](https://github.com/nestjs/passport) - Passport utilities module for Nest
- [`amqp-node/amqplib`](https://github.com/amqp-node/amqplib) - A library for making AMQP 0-9-1 clients for Node.JS
- [`open-telemetry/opentelemetry-js`](https://github.com/open-telemetry/opentelemetry-js) - A framework for collecting traces, metrics, and logs from applications
- [`siimon/prom-client`](https://github.com/siimon/prom-client) - A prometheus client for Node.js that supports histogram, summaries, gauges and counters
- [`axios/axios`](https://github.com/axios/axios) - Promise based HTTP client for the browser and node.js
- [`motdotla/dotenv`](https://github.com/motdotla/dotenv) - Dotenv is a zero-dependency module that loads environment variables from a .env
- [`PDMLab/http-problem-details`](https://github.com/PDMLab/http-problem-details) - This library implements HTTP Problem details (RFC 7807) for HTTP APIs
- [`hapijs/joi`](https://github.com/hapijs/joi) - The most powerful schema description language and data validator for JavaScript
- [`jestjs/jest`](https://github.com/jestjs/jest) - A javascript framework for testing
- [`testcontainers/testcontainers-node`](https://github.com/testcontainers/testcontainers-node) - A library to support tests with throwaway instances of Docker containers
- [`faker-js/faker`](https://github.com/faker-js/faker) - Generate massive amounts of fake (but realistic) data for testing and development
- [`florinn/typemoq`](https://github.com/florinn/typemoq) - Simple mocking library for JavaScript targeting TypeScript development
- [`ladjs/supertest`](https://github.com/ladjs/supertest) - High-level abstraction for testing HTTP
- [`eslint/eslint`](https://github.com/eslint/eslint) - ESLint is a tool for identifying and reporting on patterns found in ECMAScript/JavaScript code
- [`prettier/prettier`](https://github.com/prettier/prettier) - Opinionated Code Formatter
- [`vercel/async-retry`](https://github.com/vercel/async-retry) - Retrying made simple, easy, and async
- [`MarluanEspiritusanto/ts-mapper`](https://github.com/MarluanEspiritusanto/ts-mapper) - A mapper for mapping one object to another

## The Domain And Bounded Context - Service Boundary

- `Identity Service`: The Identity Service is a bounded context for the authentication and authorization of users using [passport](https://github.com/nestjs/passport). This service is responsible for creating new users and their corresponding roles and permissions using Jwt for authentication and authorization.

- `Flight Service`: The Flight Service is a bounded context `CRUD` service to handle flight related operations.

- `Passenger Service`: The Passenger Service is a bounded context for managing passenger information, tracking activities and subscribing to get notification for out of stock products.

- `Booking Service`: The Booking Service is a bounded context for managing all operation related to booking ticket.

Each microservice has its dependencies such as databases, files etc. Each microservice is decoupled from other microservices and developed and deployed separately. Microservices talk to each other with Rest or gRPC for synchronous calls and use RabbitMq or Kafka for asynchronous calls.

We have a separate microservice `Identity` for authentication and authorization of each request. Once signed-in users are issued a JWT token. This token is used by other microservices to validate the user, read claims and allow access to authorized/role specific endpoints.

I used [RabbitMQ](https://github.com/rabbitmq) as my MessageBroker for async communication between microservices using the eventual consistency mechanism. Each microservice uses [amqp](https://github.com/amqp-node/amqplib) to interface with [RabbitMQ](https://github.com/rabbitmq) providing, messaging, availability, reliability, etc.

Microservices are `event based` which means they can publish and/or subscribe to any events occurring in the setup. By using this approach for communicating between services, each microservice does not need to know about the other services or handle errors occurred in other microservices.

I used CQRS to decompose my features into small parts that makes our application:

- Maximize performance, scalability and simplicity.
- Easy to maintain and add features to. Changes only affect one command or query, avoiding breaking changes or creating side effects.
- It gives us better separation of concerns and cross-cutting concern (with help of mediatr behavior pipelines), instead of bloated service classes doing many things.

Using the CQRS pattern, we cut each business functionality into vertical slices, for each of these slices we group classes (see [technical folders structure](http://www.kamilgrzybek.com/design/feature-folders)) specific to that feature together (command, handlers, infrastructure, repository, controllers, etc). In our CQRS pattern each command/query handler is a separate slice. This is where you can reduce coupling between layers. Each handler can be a separated code unit, even copy/pasted. Thanks to that, we can tune down the specific method to not follow general conventions (e.g. use custom SQL query or even different storage). In a traditional layered architecture, when we change the core generic mechanism in one layer, it can impact all methods.

## How to Use Migrations
> Note: For easy using of migrations commands in typeorm, I add some scripts in `package.json` and base on these scripts we can use below commands to generate and run migrations easily.

For `generating` a new migration use this command in the root of each microservice:

```bash
npm run migration:generate -- src/data/migrations/new-migration-name
```

Also for `running` migration use this command in the root of each microservice:
```bash
npm run migration:run  
```

## How to Run


> ### Docker Compose

Use the command below to run our `infrastructure` with `docker` using the [infrastructure.yaml](./deployments/docker-compose/infrastructure.yaml) file at the `root` of the app:

```bash
docker-compose -f ./deployments/docker-compose/infrastructure.yaml up -d
```
##### Todo
I will add `docker-compsoe` for up and running whole app here in the next...

> ### Build
To `build` each microservice, run this command in the root directory of each microservice where the `package.json` file is located:
```bash
npm run build
```

> ### Run
To `run` each microservice, run this command in the root of the microservice where `package.json` is located:
```bash
npm run dev
```

> ### Test

To `test` each microservice, run this command in the root directory of the microservice where the `package.json` file is located:
```bash
npm test
```

> ### Documentation Apis

Each microservice has a `Swagger OpenAPI`. Browse to `/swagger` for a list of endpoints.

As part of API testing, I created the [booking.rest](./booking.rest) file which can be run with the [REST Client](https://github.com/Huachao/vscode-restclient) `VSCode plugin`.

## Project References & Credits

- [https://github.com/jbogard/ContosoUniversityDotNetCore-Pages](https://github.com/jbogard/ContosoUniversityDotNetCore-Pages)
- [https://github.com/nestjs](https://github.com/nestjs)

