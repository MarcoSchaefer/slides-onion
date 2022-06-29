# Infra Layer


## TOC

5.1 Repositories

5.2 Views

5.3 Entrypoints

Note: The Infrastructure Layer is the outermost layer of the Onion Architecture.
It’s responsible for implementing all the IO operations that are required for the software.
This layer is also allowed to know about everything contained in the inner layers, being able to import entities from the Application and Domain layers.
The Infrastructure Layer should not implement any business logic, as well as any use case flow.


## 5.1 Repositories

Note: A Repository is a pattern for a collection of domain objects.
It’s responsible for dealing with the persistence (such as a database), and acts like a collection of domain objects.
Usually, each domain aggregate that need to be persisted will have its own repository, so you could have a repository for Users, another for Rides, and so on.
Notice that in Onion Architecture, the database is just a infrastructure detail. The rest of your code shouldn’t worry if you are storing your data in a database, in a file, or just in memory.


## 5.2 Views (APIs, CLI, etc)

Note: The parts of your code that expose your application to the outside world are also part of the Infrastructure Layer, as they deal with IO.
The inner layers shouldn’t know if your application is being exposed through an API, through a CLI, or whatever.


```ts
const listRidesHandler = (svc: RideService, request: Request, reply: Reply) => {
  const filter = filterFromParams(request);
  const response = svc.listPreviousRides(filter);
  
  const statusCode = response.count > 0 ? OK : NOT_FOUND;
  reply.status(statusCode).send(JSON.stringify(response));
}
```


## 5.3 Entrypoints

Note:
The application’s entrypoint (usually, the main) should be responsible for instantiating all necessary dependencies and injecting them into your code.
If you have a repository that expects a PostgreSQL client, the main should instantiate it and pass it to the repository during its initialization. That’s the dependency injection mechanism.


```ts
const main = (config: Config): => {
  const mongoClient = await createMongoClient(config);
  const rideRepository = newRideRepository(mongoClient);

  const rideService = newRideService(rideRepository);
  const rideHandler = newRideHandler(rideService);

  const routes: RouteOptions[] = [
    rideRoute(rideHandler),
  ];

  const server = newServer(routes);
  await server.listen(config.port, config.host);
};
```
