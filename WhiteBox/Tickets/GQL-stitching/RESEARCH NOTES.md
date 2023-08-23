---
sticker: emoji//1f347
---
![[Pasted image 20230817171922.png]]
*It seems to be possible to use this package or technique to solve conflicts between our schemas.*



---
**GraphQL Stitching** seems to be a suitable solution to the problem described.

# <span style='color:#eb3b5a'>Problem</span>:
PC has problems when DMS and BAL have similar or identical types, mutations and enums in their **GraphQL schemas**.

When trying to initialize BAL and DMS faker services on PC, an error occurs.
It is desired to consolidate the DMS and BAL schemas without significantly altering the code on PC.

**Proposed solution**: GraphQL Stitching
**Why Stitching**: Stitching allows you to combine multiple GraphQL schemas into one. This could be useful when you have **similar or identical types**, **mutations** and **enums** in different schemas and you want to avoid conflicts.

The stitching process in GraphQL Tools v7 has been significantly improved with the introduction of the [automatic merging strategy](https://the-guild.dev/graphql/stitching/docs/getting-started/duplicate-types#automatic-merge). 

### Automatic Merge:

Types with the same name are automatically merged by default.
Objects, interfaces and input objects with the same name will merge their fields through subschemas.

If there is conflicts we can manually resolve differences between conflicting types with an onTypeConflict handler.

Since the main problem is that DMS and BAL might have identical types, mutations and enumerations, and this causes PC failures, the automatic merge strategy seems to be the most suitable solution. This strategy will automatically merge types with the same name and consolidate their fields and members, avoiding conflicts.

In addition, the merge validations will allow you to make sure that the merged schemas are consistent and do not contain errors.

### Steps to follow:
- Test with the **Ruby package** 💎: Perform a test using the Ruby package for GraphQL Stitching. 

- Evaluate the impact: Before making significant changes, it is crucial to evaluate the impact on the existing PC schema builder. If the changes are too large, we may need to consider refactoring PC or looking for another solution.

