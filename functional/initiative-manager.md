## Initiative Manager Workflow, Functional Style

```js
// get the site instance
const site = await fetchSite(domain, context.requestOptions);

// determine if UX show's Create New Initiative
const canAddInitiative = getPermission(site, "addInitiative", context.currentUser);
// returns true b/c Julianna is a member of the Env Dept Group, which has been assigned
// the `addInitiative` permission

// select group via arcgis-hub-gallery, then get the IGroup
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// create the inititive in the context of the site
// this adds the typekeyword `site:<siteId>`, "associating" it to the initiative
// it's not automatically shared to any groups
const initiative = await createInitiative({
  title: "Green Gardens",
  ...props...
  shareTo: [envDeptGroup.id],
  associateWith: {type: "site", id: site.id}
}, context.userRequestOptions);

// notify group members
// should this be done internal to the .create method, notifying members of the shareTo groups?
// or leave this as opt-in?
await sendNotification(envDeptGroup.id, newItemCreatedMessage, context.requestOptions);

// determine if UX show's Manage Permissions
const canManagePermissions = getPermission(initiative, "managePermissions" , context.currentUser);

// choose group to grant "Add Project" to
const communityGroup = await getGroup('cmtyGroupId', context.requestOptions);

// add the permission
initiative.permissions = addPermission(initiative.permissions, "addProject", "group", "communityGroupId");

// persist changes
await saveInitiative(initiative, context.userRequestOptions);

// send notification to community group
await sendNotification(communityGroup.id, newPermissionAddedBody, context.requestOptions);

// add collection to Initiative Catalog
initiative.catalog = addCollection(initiative.catalog, {
  key: "communityprojects",
  label: "Community Projects",
  scope: {
    targetEntity: "item",
    filters: [
      {
        predicates: [
          {
            group: "approvedCommunityGroupContentId",
            type: "Hub Project"
            tag: "Approved" // future: category: "Approved"
          }
        ]
      }
    ]
  }
});

initiative.catalog = addCollection(initiative.catalog, {
  key: "communitygroups",
  label: "Community Groups",
  scope: {
    targetEntity: "group",
    filters: [
      {
        predicates: [
          {
            typekeyword: `initiative:<initiativieId>`
          }
        ]
      }
    ]
  }
});

// persist changes
await saveInitiative(initiative, context.userRequestOptions);
```
