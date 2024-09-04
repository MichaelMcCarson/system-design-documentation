# Naming Conventions

For standardizing between the front and back end, the following naming conventions apply:

## Grouping Container | Class-like

When a file will be used as a grouping container i.e. class then the default means of naming the file should be PascalCase with the use-case as a suffix.

For example:

- UserController.ts and UserService.ts
  Within the import, the file is imported as

  ```ts
  import * as UserService from '../services/UserService';
  ```

  and used like UserService.listAll(...);

## All other Files

When a file will be used to export individual functions then the default means of naming the file should be camel case without a suffix.
