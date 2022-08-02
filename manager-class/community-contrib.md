## Community Contributor Workflow, Manager Class Style

```js
// create initiative manager
const initiativeManager = HubInitiativeManager.create(context);

// load green-gardens initiative
const greenGardensPOJO = initiativeManager.fetch('green-gardens');

// Can Adam create a Project?
const canCreateProject = initiativeManager.getPermission(greenGardensPOJO, 'addProject');
// returns true b/c Adam is member of the CityX Community Group

// create project in initiative context
// this adds the typekeyword `initiative:<greenGardensId>`, "associating" it to the initiative
// it's not automatically shared to any groups
const pineStGardenProjectPojo = await initiativeManager.createProject(greenGardensPOJO,{
  title: "Pine St Garden",
  geometry: {...},
  shareTo: [],
  capabilities: ["Events", "Discussions"]
});

// To work with the proejct, we create a manager
const projectMgr = HubProjectManager.create(context);

// alternatively we could do
pineStGardenProjectPojo.capabilities = ["Events", "Discussions"];
await projectMgr.save(pineStGardenProjectPojo);

// Later when Adam creates a group, he can share this project to it via
await projectMgr.shareToGroup(pineStGardenProjectPojo, "3ef", {notify: true});
// which could also send notifications

// Create Event
// this checks that the project has `Event` capability, and user has permissions
const meetup = await projectMgr.createEvent(pineStGardenProjectPojo, {
  title: "Pine St Garden Planning Meetup",
  ...
});


// Create Discussion
// this checks that the project has `Event` capability, and user has permissions
const post = await projectMgr.createPost(pineStGardenProjectPojo, {
  title: "How can we make this Garden useful to our neighborhood?",
  body: "...",
  discussion: "hub://item/<pineStGardenProjectPojo.id>"
});


```
