## Community Contributor Workflow, OOP Style

```js
// create instance of the initiative
const greenGardensInitiative = await HubInitiative.fetchById(id, context);

// Can Adam create a Project?
const canCreateProject = greenGardensInitiative.getPermission('addProject');
// returns true b/c Adam is member of the CityX Community Group

// Create project in initiative context
// this adds the typekeyword `initiative:<greenGardensId>`, "associating" it to the initiative
// it's not automatically shared to any groups
const pineStGardenProject = await greenGardensInitiative.createProject({
  title: "Pine St Garden",
  geometry: {...},
  shareTo: [],
  capabilities: ["Events", "Discussions"]
});


// alternatively we could do
pineStGardenProject.capabilities = ["Events", "Discussions"];
await pineStGardenProject.save();

// Later when Adam creates a group, he can share this project to it via
await pineStGardenProject.shareToGroup("3ef", {notify: true});
// which could also send notifications

// Create Event
// this checks that the project has `Event` capability, and user has permissions
const meetup = await pineStGardenProject.createEvent({
  title: "Pine St Garden Planning Meetup",
  ...
});

// Create Discussion
// this checks that the project has `Event` capability, and user has permissions
const post = await pineStGardenProject.createPost({
  title: "How can we make this Garden useful to our neighborhood?",
  body: "...",
  discussion: "hub://item/<pineStGardenProjectPojo.id>"
});

```
