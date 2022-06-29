# Application Layer


## TOC

4.1 Application services

4.2 Interfaces

4.3 DTOs

Note: The Application Layer is the second most inner layer of the architecture.
This layer is responsible for preparing the environment for your models, so they can execute their business rules.
The application layer implements services (sometimes called use cases) instead of Business rules.


## 4.1 Application services (or use cases)


```ts
const async scheduleRide = (
  userRepository: UserRepository,
  rideAPI: RideAPI,
  notificationAPI: NotificationAPI,
  userId: string,
  destination: Address,
) : ScheduledRide | Error => {
  const user = await userRepository.get(userId);

  if (!user) return Error.UserNotFound;
  if (!user.canScheduleRide()) return Error.ActionForbidden;

  if (user.shouldNotifyLovedOnes(Action.ScheduleRide)) {
    notificationAPI.notify(user.lovedOnes);
  }

  return await rideAPI.schedule(user, destination);
};
```

Note: When talking about the domain layer, we saw a hypothetical User model, which contains the data that represents an user, and we also described some of it's business rules.
That's our domain definition. Now we want to implement a use case.
The use case we want to implement is, given an user that wants to schedule a ride, I want to check if they can schedule it, and also would like to notify their LovedOnes if necessary.


## 4.2 Interfaces

Note: Another important topic regarding the Application Layer, is the use of interfaces.
Since this Layer will be responsible for describing use cases, it will, most of the times, have to receive an interface for something external, like a repository or an API client.
So this layer needs to create interfaces that describe the dependencies they expect to receive


```ts
interface UserRepository {
  get: (userId: string) => Promise<User | null>;
}

interface NotificationAPI {
  notify: (lovedOnes: LovedOne[]) => Promise<void>;
}

interface RideAPI {
  schedule: (user: User, destination: Address) => Promise<ScheduledRide>;
  list: (user: User, filter: Filter) => Promise<ScheduledRide[]>;
}
```


## 4.3 DTOs

Note:
A Data Transfer Object (DTO) is an object that contains data that will be transferred between different layers, in some specific format.
Sometimes you want to transfer data that is not exactly a Domain Model, or a Value Object.
For example, there will be situations where we need to receive a payload as an input to our use case.


```ts
const async listPreviousRides = (
  userRepository: UserRepository,
  rideRepository: RideRepository,
  userId: string,
  filter: Filter,
): Promise<Response> => {
  const user = await userRepository.get(userId);
  if (!user) return Error.UserNotFound;

  const rides = await rideRepository.list(user, filter);

  return {
    rides,
    count: rides.length,
    filter,
  }
};
```

Note:
In this case, we are receiving a DTO called filter, which contain parameters that refine our list method, and we are returning a Response DTO, which has a specific format for this use case.


```ts
type Filter = {
  date?: Date;
  destination?: Address;
};

type Response = {
  rides: ScheduledRide[];
  count: number;
  filter: Filter;
};
```
