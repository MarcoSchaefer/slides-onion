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
const async recalculatePositions = (
  positionRepository: PositionRepository,
  price: number,
  date: Date,
  securityId: string,
) => {
  const positions = await positionRepository.get(securityId, date);
  const recalculatedPositions = positions.map(p => recalculate(p, price));

  await positionRepository.update(recalculatedPositions);
};
```

Note: When talking about the domain layer, we saw a hypothetical Position model, which contains the data that represents a position, and we also described how to recalculate a position if the price changes.
That's our domain definition. Now we want to implement a use case.
The use case we want to implement is, given that a price changed for a particular security, I want to recalculate the positions for all users that had the same security on the provided date.


## 4.2 Interfaces

Note: Another important topic regarding the Application Layer, is the use of interfaces.
Since this Layer will be responsible for describing use cases, it will, most of the times, have to receive an interface for something external, like a repository or an API client.
So this layer needs to create interfaces that describe the dependencies they expect to receive


```ts
interface PositionRepository {
  get: (securityId: string, date: Date) => Promise<Position[]>;
  update: (positions: Position[]) => Promise<void>;
}
```


## 4.3 DTOs

Note:
A Data Transfer Object (DTO) is an object that contains data that will be transferred between different layers, in some specific format.
Sometimes you want to transfer data that is not exactly a Domain Model, or a Value Object.
For example, there will be situations where we need to receive a payload as an input to our use case.


```ts
const async listPositions = (
  positionRepository: PositionRepository,
  filter: Filter,
): Promise<Response> => {
  const positions = await positionRepository.get(filter);

  return {
    positions,
    count: positions.length,
    filter,
  }
};
```

Note:
In this case, we are receiving a DTO called filter, which contain parameters that refine our list method, and we are returning a Response DTO, which has a specific format for this use case.


```ts
type Filter = {
  fundId: string;
  portfolioRelations: Set<string>;
  productType?: ProductType;
  assetClass?: AssetClass;
  minDate?: string;
  maxDate?: string;
  securityName?: string;
  brokerName?: string;
};

type Response = {
  positions: Position[];
  count: number;
  filter: Filter;
};
```
