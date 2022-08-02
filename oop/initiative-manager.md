## Initiative Manager Workflow, OOP Style

```js
// get the site instance
const site = await HubSite.fetchByDomain(domain, context);

// determine if UX show's Create New Initiative
const canAddInitiative = site.getPermission("addInitiative");
// returns true b/c Julianna is a member of the Env Dept Group, which has been assigned
// the `addInitiative` permission

// select group via arcgis-hub-gallery, then get the IGroup
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// create the inititive in the context of the site
const initiative = await site.create("Initiative", {
  title: "Green Gardens",
  ...props...
  shareTo: [envDeptGroup.id]
});

// notify group members
// should this be done internal to the .create method, notifying members of the shareTo groups?
// or leave this as opt-in?
await site.sendNotification(envDeptGroup.id, newItemCreatedMessage);

// determine if UX show's Manage Permissions
const canManagePermissions = initiative.getPermission("managePermissions");

// choose group to grant "Add Project" to
const communityGroup = await getGroup('cmtyGroupId', context.requestOptions);

// add the permission
initiative.addPermission("addProject", "group", "cmtyGroupId");

// persist changes
await initiative.save()

// send notification to community group
// should this be send in .addPermission?
await initative.sendNotification(communityGroup.id, newPermissionAddedBody);

// add collection to Initiative Catalog
initiative.catalog.addCollection({
  key: "communityprojects",
  label: "Community Projects",
  scope: {
    targetEntity: "item",
    filters: [
      {
        predicates: [
          {
            group: "approvedCommunityContentGroupId",
            type: "Hub Project"
          }
        ]
      }
    ]
  }
});

initiative.catalog.addCollection({
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
await initiative.save()



```
