## Initiative Manager Workflow, Manager Class Style

```js
// create site manager instance
const siteMgr = await HubSiteManager.init(context);

// get the site instance
const sitePOJO = siteMgr.fetchByDomain(domain);


// determine if UX show's Create New Initiative
const canAddInitiative = siteMgr.getPermission(sitePOJO, "addInitiative");
// returns true b/c Julianna is a member of the Env Dept Group, which has been assigned
// the `addInitiative` permission

// select group via arcgis-hub-gallery, then get the IGroup
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// create initiative manager
const initiativeManager = HubInitiativeManager.create(context);
// create the inititive
const initiativePOJO = await initiativeManager.create({
  title: "Green Gardens",
  ...props...
  shareTo: [envDeptGroup.id]
});

// notify group members
// should this be done internal to the .create method, notifying members of the shareTo groups?
// or leave this as opt-in?
await initiativeManager.sendNotification(envDeptGroup.id, newItemCreatedMessage);

// determine if UX show's Manage Permissions
const canManagePermissions = initiativeManager.getPermission(initiativePOJO, "managePermissions");

// choose group to grant "Add Project" to
const communityGroup = await getGroup('cmtyGroupId', context.requestOptions);

// add the permission
initiativeManager.addPermission(initiativePOJO, "addProject", "group", "communityGroupId");


// persist changes
await initiativeManager.save(initiativePOJO);

// send notification to community group
await initiativeManager.sendNotification(communityGroup.id, newPermissionAddedBody);

// add collection to Initiative Catalog
initiativeManager.addCollection(initiativePOJO, {
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

initiativeManager.addCollection(initiativePOJO, {
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
await initiativeManager.save(initiativePOJO);

```
