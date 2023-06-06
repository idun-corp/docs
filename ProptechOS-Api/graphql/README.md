# GraphQL API (Preview)

**GraphQL** is a query language for APIs and a runtime for fulfilling API queries. GraphQL provides a complete and understandable description of the data in your graph, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools. For more information please visit [GraphQL Page](https://graphql.org/).
GraphQL API is available in our Preview API via the new endpoint:

`POST /preview/graphql`

## GraphQL API Try Out

To try out the new GraphQL API graphiql page can be used. This test page is available via **<BASE_PROPTECHOS_URL>/preview/graphiql** .

![GraphQl Page](../../images/p8s_graphql_try_page.png)

In order to start, the Authorization header with Bearer token in JSON format should be provided in _“Header”_ section (Bearer token can be copied from the Swagger page).  Authorization header example:

````
{"Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGci..."}
````

More information how to obtain access token, please find here: [ProptechOS Authentication](https://github.com/idun-corp/docs/blob/main/ProptechOS-Api/authentication/README.md#authentication)

## Examples of some possible requests to start:

### 1. Get paged Devices

```
{
  getDevicesPaged { 
    totalElements 
    content { 
      id 
      isMountedInBuildingComponent { 
        id 
        isPartOfBuilding { 
          id 
          isPartOfRealEstate { 
            id 
            ownedBy { 
              id 
            } 
          } 
        } 
      } 
    } 
  }
}
```

### 2. Get paged Devices with specified page and page size:

```
{
  getDevicesPaged(filter: {pageFilter: {page: 5 size: 50}}) { 
    totalElements 
    content { 
      id 
      isMountedInBuildingComponent { 
        id 
        isPartOfBuilding { 
          id 
          isPartOfRealEstate { 
            id 
            ownedBy { 
              id 
            } 
          } 
        } 
      } 
    } 
  }
}
```

### 3. Get Device by id

```
{
  getDevice (id: "3fa85f64-5717-4562-b3fc-2c963f66afa6") { 
    id 
    popularName 
    class 
    littera 
    createdTime 
    createdByAgent 
    isMountedInBuildingComponent { 
      id 
      popularName 
      class 
      littera 
      createdTime 
      createdByAgent 
      isPartOfBuilding { 
        id 
        popularName 
        class 
        littera 
        createdTime 
        createdByAgent 
        isPartOfRealEstate { 
          id 
          popularName 
          class 
          littera 
          createdTime 
          createdByAgent 
          ownedBy { 
            id 
            popularName 
            class 
            createdTime 
            createdByAgent 
          } 
        } 
      } 
    } 
  }
}
```


