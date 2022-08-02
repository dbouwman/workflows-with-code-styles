## Site Manager Workflow, Manager Class Style

```js
// create site manager instance
const siteMgr = await HubSiteManager.init(context);

// get the site instance
const sitePOJO = siteMgr.fetchByDomain(domain);

// Get list of Groups Paige can "see" - not restricted to groups she is a member or admin in
const groups = await hubSearch(...groupQuery...);

// Paige selects "Environmental Department" group in UI, which returns the id

// get the Environment Department Group
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// Associate "addInitiatives" permission to with "Env Dept Group" for Site
siteMgr.addPermission(sitePOJO, "addInitiative", "group", "envDeptGroupId");

// Associate "addProject" permission to with "Env Dept Group" for Site
siteMgr.addPermission(sitePOJO, "addProject", "group", "envDeptGroupId");

// Add group w/ type subset to item scope in Catalog
siteMgr.addScopeFilter(sitePOJO, "item", {
    // key is needed so we can look up the entry to make changes later
    // TBD exactly how this key is constructed
    key: "addInitiative:envDeptGroupId",
    predicates: [
      {
        group:"envDeptGroupID",
        type: { any: ["Hub Initiative", "Hub Project"]
      }
    ]
  }

// save changes
await siteMgr.save(sitePOJO);

// send notification re: new access - likely additional params
await siteMgr.sendNotification("envDeptGroupId", theMessageBody);
```
