# single-view-service-prototype

Single View V2 data consolidation service (via GraphQL)

# Description

This server is meant to consolidate data from disparate sources in
Hackney ecosystem under one GraphQL interface, taking the business
logic burden from the frontend.

The project is based on [these quidelines](https://dev.to/memark/running-a-graphql-api-in-net-6-on-aws-lambda-17oc)
using [HotChocolate GraphQL suite](https://chillicream.com/docs/hotchocolate).
It uses [Serverless](https://www.serverless.com/) PaaC framework for AWS deployment.

The code in Service\Boundary\HackneyApi is generated by NSwag from the
files in Service\Boundary\Swagger automatically from REST Swagger
specifications.

At the time of writing, the server is integrated with HousingSearchAPI. A client for
PersonAPI is generated but not yet incorporated in GraphQL structure.

Note that the file Service\Boundary\Swagger\Hackney-housingSearchApi-1.0.0-resolved.json
has been downloaded from Swaggerhub and then manually edited to
accommodate PersonTypes that are returned by the HousingSearchAPI server
but not documented in swagger specification.

# Developing

Add root resolvers to Query class, and further levels of resolvers as members of
the result type. More information in
[HotCholoate user guide](https://chillicream.com/docs/hotchocolate/defining-a-schema).

# Running

Configure your desired API endpoint URLs in
`Service\appsettings.Development.json`. Alternatively, set up any
other ASP.NET configuration source, such as environment, with the same
parameters.

Open the project in Visual Studio and run Service local. Browser window opens up automatically with GraphQL client.

Alternatively, use `dotnet run` command line and visit local page at `http://localhost:_port_/graphql`.

Create a new query document. At the bottom of the page, set up Authorisation header with your Hackney Google JWT token.

Add queries such as the example below to the client window:

```graphql
query SearchPersons($search: String!) {
  search {
    persons(searchText: $search, pageSize: 40) {
      total
      page {
        title
        firstname
        surname
        dateOfBirth
        personTypes
      }
    }
  }
}
```

This query is parametrised, so to run it, a parameter is required:
```json
{
  "search": "john"
}
```

Parameters can be provided at the bottom of the page.

Execute the query, explore the results, explore and download GraphQL schema.

# Deployment

To build the project, we use Amazon lambda tools. Install them using

```
dotnet tool install amazon.lambda.tools --version "5.*"
```

Build the project using
```
dotnet lambda package -o dist/Service.zip
```

Deploy with serverless using
```
npx serverless --stage development deploy
```

# TODO

API endpoint configuration environment is not currently being picked up from
AWS Parameter store. The [deployed service](https://r7owsn3td2.execute-api.eu-west-2.amazonaws.com/graphql/)
is performing correctly with the value provided directly in lambda environment.
