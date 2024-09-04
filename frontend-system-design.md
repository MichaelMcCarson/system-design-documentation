# Frontend System Design

The name used for each swimlane needs to be carefully decided on and will be ushered into the ubiquitous language of the company.

If a swimlane contains multiple concerns, begins to be too large for a single swimlane, or will have sub-parts that are tangentially related, then it should be broken up into multiple swimlanes with a greater granularity on what it actually is for. Remember, swimlanes are intended to be libraries; if a swimlane does too much, we need to reevaluate the swimlane's core principles and tenets.

## Structural Swimlanes

Some of the _structural swimlanes_ that are in a project by default are:

- environments
- identity¹
- shared
- app-shell

¹ The identity swimlane isolates users and groups access along with deals with all things authorization, SSO, priveleged account management, etc.

## Major grouping types

Each one of the following are _major_ grouping folders¹ found within the swimlane grouping folder:

- api
- application
- data-access
- domain
- feature²
- util
- ui
- shell³
- shared

¹ Grouping folders are singular because if it was a library (which it is intended to be micro-libraries) it would be singular; this does not apply to the _minor_ grouping folders below because they would be within the library.

² `feature/` can occur within a swimlane and is best described as the parts that sum to the whole; almost akin to working components. For example, let's say we need smart components for "Resources", "Guides", "Events", and "Groups"—we would use this folder for each variation. This presumes that the different smart components, in this case, conform to [Separate Ways](https://semaphoreci.com/blog/domain-driven-design-microservices) and require their own components.

³ A reusable _shell_ to load similar features into a swimlane or the entire app. The app-shell is it's own swimlane since its a top-level concern. The shell can be at the feature level as well; see [InDepth: Shell Library patterns with Nx and Monorepo Architectures](https://indepth.dev/posts/1117/the-shell-library-patterns-with-nx-and-monorepo-architectures).

The grouping types as well as sub-grouping folders are not always required folders, and if there are times that it does not make sense to add them, then don't.

## Grouping type access order

A high-level principle to keep in mind when developing an application is the order of dependencies. Each "grouping type" has a specific place it fits into the flow. This ordering is as follows.

```
Depends
  On
  |      API ¹
  |      Shell
  |      Feature
  |      Application
  |      UI
  |      Data Access
  |      Domain
  |      Util
  |      Shared UI ²
  V      Shared Util ²
```

¹ Within a swimlane, API is at the top of the dependency tree, meaning it can access allow layers below it. However when importing from other swimlanes, API can be considered equal to feature, meaning it can be imported in features.

² "Shared" libs consist of UI and Util elements that are independent of a swimlane, thusly they will always be at the bottom of the dependency ordering.

## Grouping types explained

### API

The API is the main entry point into a swimlane from outside the swimlane. Swimlanes should only export to the outside application those things that are intended to work outside the swimlane. Since an API layer helps to establish separation of concerns between swimlanes, all exports to other swimlanes, and imports from another swimlane MUST be done through the API layer.

```
├── api¹
|   ├── store-api²
|   ├── index.ts³
```

¹ If this was a true micro-library structure, then every API would be the only library that any other libraries can interact with and would be the source of where the exporting occurs for every swimlane; this way the other libraries do not need to import any other type of library and only need to import the `api/` library.

² Facades for interacting with the `domain/store/`. The data itself resides within the `domain/store`, but interacting with the store occurs through facades for specific use cases.

³ Export all objects from this file for within the entire swimlane.

### Application

The Application layer is part of the Domain layer in DDD. This is where application concerns live, such as central business logic and services, component state, routing concerns, and more.

We've separated Application from Domain for two main reasons:

1. Removing this from domain allows us to place this above the "UI" layer in the dependency graph (see [the chart](#grouping-type-access-order) for reference), and thus we can move logic into this layer, such as UI logic and feature logic.
2. This layer can tend to grow much bigger than the others since it's where the aforementioned logic will live.

```
├── application¹
|   ├── dataAccessHooks
|   ├── entities²
|   ├── routing
|   ├── services
|   ├── stateHooks
|   ├── store³
|   ├── testing
|   ├── validations
```

¹ This is not an exhaustive list of possible sub-groupings that can appear under application. However, this doesn't mean any possible adhoc grouping should be used.

² Refers specifically to application-level entities, not domain-level entities, such as form models.

³ The `store/` folder is for state management. This is only applicable when the state management is internally within the application and there is no need to go externally, e.g. to redis or memcached—in those instances, it would be part of the data-acess layer. Direct access to state management only occurs through the API (facades). The inner workings of the store are kept internal to these swimlanes, allowing easy swapping out of the underlying implementation.

### Data Access

Data access deals with any form of going outside the app to retrieve data. This is typically your HTTP calls or Database queries.

```
├── data-access
|   ├── specific-grouping-type¹
```

¹ When like requests start to become multiple files, it may make sense to add a grouping folder for these requests. This is optional, not the default. In general, the swimlanes should be granular enough to avoid an overwhelmingly large flat structure.

### Domain

Domain is the core of any swimlane. This is where things like core models, configs, and infrastructure concerns live.

```
├── domain¹
|   ├── configs²
|   ├── entities
|   ├── infrastructure
```

¹ The domain section may grow over time to encompass more sub-grouping folders.

² The `configs/` folder in this case is not to be confused wih `environments/configs/`. Any going out of the application and into the underlining infrastructure for environment variables should not be done in these configs, but rather in the `environments/configs/` grouping folder (this includes the `shared/` swimlane).

### Feature

Feature is where the core functionality of the app lives. Each feature usually contains only the code specific to that feature, and almost acts as an "entry point" to generic UI pieces.

```
├── <swimlane>
|   ├── feature¹
|   |   ├── feature-one
|   |   ├── feature-two
```

¹ With our current grouping folders, the feature grouping folders contains all the working parts (_smart components_) that go into making the swimlane work as intended.

### Shell

Shell is an optional grouping where a composition of features lives. This can create an entry point into features, compose routing trees, and other things of the sort that requires smart component composition.

```
├── feature-shell¹
|   ├── routes
```

¹ The shell is a flexible grouping type, its contents depend heavily on then business needs and specific feature points and how things need to be composed.

### Util

Util is where swimlane-specific utilities live. These would be things that are exclusively not shareable outside the swimlane, as those would fall under the jurisdiction of shared-util. If util

```
├── util
|   ├── desktop¹
|   ├── mobile¹
|   ├── specific-grouping-type²
```

¹ Most applications separate mobile and desktop explicit features into their own apps. At the least, explicit desktop and mobile groupings should be encapsulated.

² When multiple files start to overlap in purpose, it may make sense to add a grouping folder for these files. This is optional, not the default. In general, the swimlanes should be granular enough to avoid an overwhelmingly large flat structure. 

### UI

UI is where generic swimlane-specific UI elements lives. These would be things that are exclusively not shareable outside the swimlane, as those would fall under the jurisdiction of shared-ui.

```
├── ui
|   ├── specific-grouping-type¹
```

¹ When multiple files start to overlap in purpose, it may make sense to add a grouping folder for these requests. This is optional, not the default. In general, the swimlanes should be granular enough to avoid an overwhelmingly large flat structure. 

## Barrelling

Barrelling should be explicit and show intent. Anything exported by one "lego" for other "legos" to access should be in the barrel's `index.ts` file. For example:

```ts
export { fontFamily } from './globalStyle';
export * from './text';
export { default as theme } from './theme';
export type { Theme } from './theme';
```

## Example Folder Structure

Note: `...` represents a condensed view of the structured major grouping folders.

```
├── src
|   ├── app-shell
|   |   ├── ...
|   ├── identity
|   |   ├── ...
|   ├── environments
|   |   ├── domain
|   |   |   ├── configs
|   |   |   ├── entities
|   |   ├── util
|   ├── <swimlane>
|   |   ├── api
|   |   |   ├── store-api
|   |   |   ├── index.ts
|   |   ├── domain
|   |   |   ├── configs
|   |   |   ├── entities
|   |   |   ├── store
|   |   |   ├── validations
|   |   ├── data-access
|   |   ├── feature
|   |   |   ├── <feature-name>
|   |   ├── ui
|   |   ├── util
|   |   |   ├── desktop
|   |   |   ├── mobile
|   |   ├── index.ts
|   ├── <swimlane_two>
|   |   ├── ...
|   ├── <swimlane_three>
|   |   ├── ...
|   ├── shared
|   |   ├── domain
|   |   |   ├── configs
|   |   |   ├── entities
|   |   ├── ui
|   |   ├── util
|   ├── index.tsx
```
