## Site Manager Workflow, OOP Style

```js
// get the site instance
const site = await HubSite.fetchByDomain(domain, context);

// Get list of Groups Paige can "see" - not restricted to groups she is a member or admin in
const groups = await hubSearch(...groupQuery...);

// Paige selects "Environmental Department" group in UI, which returns the id

// get the Environment Department Group
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// Associate "addInitiatives" permission to with "Env Dept Group" for Site
site.addPermission("addInitiative", "group", "envDeptGroupId");

// Associate "addProject" permission to with "Env Dept Group" for Site
site.addPermission("addProject", "group", "envDeptGroupId");

// Add group w/ type subset to item scope in Catalog
site.catalog.addScopeFilter("item",
  {
    // key is needed so we can look up the entry to make changes later
    // TBD exactly how this key is constructed
    key: "addInitiative:envDeptGroupId",
    predicates: [
      {
        group:"envDeptGroupID",
        type: { any: ["Hub Initiative", "Hub Project"]
      }
    ]
  });

// save changes
await site.save();

// send notification re: new access - likely additional params
await site.sendNotification("envDeptGroupId", theMessageBody);
```
