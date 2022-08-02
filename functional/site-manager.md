# Site Manager Workflow, Functional Style

```js
// get the site
const site = await fetchSite(domain, context.requestOptions);

// Get list of Groups Paige can "see" - not restricted to groups she is a member or admin in
const groups = await hubSearch(...groupQuery...);

// Paige selects "Environmental Department" group in UI, which returns the id

// get the Environment Department Group
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// add an entry to the permissions array, ensuring uniqueness
site.permissions = addPermission(site.permissions, {
  // TODO: consider splitting permission into action and entity so we can streamline coupling into catalog
  permission: "addInitiative",
  type: "group",
  id: "envDeptGroupId"
});

site.permissions = addPermission(site.permissions, {
  permission: "addProject",
  type: "group",
  id: "envDeptGroupId"
});

// add scope filter to the catalog
site.catalog = addScopeFilter(site.catalog, "item", {
  // key is needed so we can look up the entry to make changes later
  // TBD exactly how this key is constructed
  key: "addInitiative:envDeptGroupId",
  predicates: [
    {
      group:"envDeptGroupID",
      type: { any: ["Hub Initiative", "Hub Project"]
    }
  ]
})

// update the site
await updateSite(site, context.userRequestOptions);

// send notification re: new access - likely additional params
await sendGroupNotification(["envDeptGroupId", theMessageBody, context.requestOptions);
```
