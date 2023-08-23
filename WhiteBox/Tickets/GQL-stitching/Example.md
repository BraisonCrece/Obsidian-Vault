---
sticker: emoji//1f440
---
Imagine we have two different systems:

## Human Resources (HR) System: 
This system manages employee information, such as **names**, **roles**, and **salaries**.

## Project System (PS): 
This system manages **projects**, **employee** **assignments** to projects and **hours worked** on each project.

Both systems have their own <span style='color:#8854d0'>GraphQL APIs</span>.

### GraphQL schema of the Human Resources System (HR):
```graphql
type Employee {
  id: ID!
  name: String!
  role: String!
  salary: Float!
}

type Query {
  employeeById(id: ID!): Employee
}
```

### GraphQL schema of the Project System (PS):
```graphql
type Employee {
  id: ID!
  name: String!
  projectAssigned: Project
  hoursWorked: Int!
}

type Project {
  name: String!
  description: String!
}

type Query {
  employeeByProject(projectName: String!): [Employee].
}
```

Problem:
If you observe, both systems define an **Employee type**, but with different fields. If you try to combine these two schemas directly into a single GraphQL API (for example, for the Piranha Client (PC) in our case), you would have conflicts due to duplicate definitions.

#### Possible Solution: <span style='color:#8854d0'>GraphQL Stitching
</span>
With stitching, we can merge these two schemas into one, consolidating the Employee type and avoiding conflicts.

##### Merged Schema:
```graphql
type Employee {
  id: ID!
  name: String!
  role: String!
  salary: Float!
  projectAssigned: Project
  hoursWorked: Int!
}

type Project {
  name: String!
  description: String!
}

type Query {
  employeeById(id: ID!): Employee
  employeeByProject(projectName: String!): [Employee].
}
```

With this merged schema, we can make queries that combine data from both systems. For example, you could get an employee's name, role, salary, and assigned project in a single query.

What does stitching do?
<span style='color:#3867d6'>Combines types with the same name</span>: In our example, the Employee type from both systems is merged into one.
<span style='color:#3867d6'>Consolidates fields</span>: Employee fields from both systems are consolidated into a single type.
<span style='color:#3867d6'>Handles conflicts</span>: If there were conflicts (e.g., fields with the same name but different types), stitching provides tools to resolve them. ([automatic](https://the-guild.dev/graphql/stitching/docs/getting-started/duplicate-types#automatic-merge), [manual](https://the-guild.dev/graphql/stitching/docs/getting-started/duplicate-types#manual-resolution), [auto-transforms](https://the-guild.dev/graphql/stitching/docs/getting-started/adding-transforms) )