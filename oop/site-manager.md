## Site Manager Workflow, OOP Style

### Narrative

As a Site manager (Paige),
I want to delegate responsibility for managing Initiatives + Projects to Department managers
So that they do their work independent of me
And so they can't change the Site

## Steps

1. View the Site, go to the Site manager
2. In Site manager, choose to Collaboration / Permissions
3. Choose the Environmental Department Group and select Permissions: Manage Initiatives, Manage Projects.
4. Paige indicates that Initiatives + Projects created in the group are searchable in the Site (catalog)
5. Send a notification to the Group that they now have access and include links to get started
6. ~Paige receives notifications as Initiatives are created~ moved to when user creates content

## Code

```js
import {HubSite, ArcGISContextManager} from "@esri/hub-common";
import {UserSession} from "@esri/arcgis-rest-request";

// create session and context
// in opendata-ui, this is all done for you, and exposed via appSettings.context
const session = new UserSession({username: "dave", password: "seekret"});
const ctxMgr = await ArcGISContextManager.create({authentication:session});
const context = ctxMgr.context;

// get the site instance
const site = await HubSite.fetchByDomain(domain, context);

// Get list of Groups Paige can "see" - not restricted to groups she is a member or admin in
const groups = await hubSearch(...groupQuery...);

// Paige selects "Environmental Department" group in UI, which returns the id

// get the Environment Department Group
const envDeptGroup = await getGroup('envDeptGroupId', context.requestOptions);

// Associate "addInitiatives" permission to with "Env Dept Group" for Site
site.permissions.add("addInitiative", "group", "envDeptGroupId");

// Associate "addProject" permission to with "Env Dept Group" for Site
site.permissions.add("addProject", "group", "envDeptGroupId");

// Add group w/ type subset to item scope in Catalog
site.catalog.addScopeFilter("item",
  {
    // key is needed so we can easily look up the entry to make changes later
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
// Note: .sendNotification is not implemented yet
await site.notifications.send("envDeptGroupId", theMessageBody);
```
