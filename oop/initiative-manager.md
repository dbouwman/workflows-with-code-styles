## Initiative Manager Workflow, OOP Style

### Narrative

As an Initiatives Manager (Julianna),
I want to create Initiatives and allow the public to recommend Projects
So that I can align community efforts with city goals
And I can oversee their progress + performance

### Workflow

1. Julianna visits the Site and sees option to "Create new Initiative"
1. Julianna enters the name ("Green Gardens") and the geography for the city
   - Future: Julianna can draw multiple locations or choose a dataset of allowable green spaces
1. Julianna then sees options for who is managing this initiative: Use Existing Group, Create New Group, No Shared Editing

1. Julianna chooses "Use Existing Group" and sees the Environmental Departed update group
1. Julianna completes creation of the Initiative
1. Julianna goes to Initiatives > Collaborate and sees the Env. Dept Group in Permissions for managing the Initiative
1. Julianna adds Permission:
   - Add Projects: Community Group
   - Add Groups: Community Group (start garden clubs) // I'm not quite sure about this as a permission
1. Julianna adds Collections to Initiative Catalog
   - Community Projects collection
     - Hub Project items, with tag Approved, in Community Group
   - Community Groups Collection
     - Group, with specific ids

## Code

```js
// create session and context
// in opendata-ui, this is all done for you, and exposed via appSettings.context
const session = new UserSession({username: "dave", password: "seekret"});
const ctxMgr = await ArcGISContextManager.create(session);
const context = ctxMgr.context;

// get the site instance
const site = await HubSite.fetchByDomain(domain, context);

// determine if UX show's Create New Initiative
const canAddInitiative = site.getPermission("addInitiative");
// returns true b/c Julianna is a member of the Env Dept Group, which has been assigned
// the `addInitiative` permission

// select group via arcgis-hub-gallery, then get the IGroup
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// create the inititive in the context of the site
// the .create method will only support a small set of Hub Entities
// which are relevant in the context of the current entity.
// i.e. for a Site, `.create` will support `project, `event`, `discussion`, `initiative`
// but for a Project, it would only support `event`, `discussion`
const initiative = await site.create("initiative", {
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
// current API - kinda redundant, but we expect some changes which are easier w/ an object...
initiative.permissions.add("addProject", {
        permission: "addProject",
        target: "group",
        targetId: communityGroup.id,
      });

// once we build out the UX for permissin management we may be able to streamline to
initiative.permissions.add("addProject", "group", cmtyGroupId.id);

// persist changes
await initiative.save()

// send notification to community group
// should this be send in .addPermission?
await initative.notifications.send(communityGroup.id, newPermissionAddedBody);

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
