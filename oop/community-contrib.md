## Community Contributor Workflow, OOP Style

### Narrative

As a Community Member new to Hub (Adam)
I want to suggest new garden projects
So I can use City resources to grow my participants
And maybe get + report on grant funding

Workflow

1. Adam visits the _Green Gardens_ initiative
2. Adam chooses to "Suggest new Project"
3. Adam enters a _Pine St Garden_ as the name and draws on an approved plot of land

- I assume we are not building checks for "approved plot of land"; lets remove this if we're not implemeting as it
- Adam leaves the checkbox for "associate with _Green Gardens_ Initiative"
  - Q: So Adam is creating a project in the context of the intiiative, but not associating it with the initiative?
  - Q: What would checking the box do? stamp a typekeyword `initiative:<initiativeId>`?

4. Adam then sees options for who is managing this Project: Use Existing Group, Create New Group, No Shared Editing
   - Adam chooses "No Shared Editing" as he's starting this project independently.
   - later he'll start a new community group
5. Adam enables Events, Discussions so he can start reaching out to other public members
6. Adam creates a neighborhood Event "Pine St Garden Planning Meetup"
7. Adam adds a new Discussion "How can we make this Garden useful to our neighborhood?"

## Code

```js
// create session and context
// in opendata-ui, this is all done for you, and exposed via appSettings.context
const session = new UserSession({username: "dave", password: "seekret"});
const ctxMgr = await ArcGISContextManager.create(session);
const context = ctxMgr.context;

// create instance of the initiative
const greenGardensInitiative = await HubInitiative.fetchById(id, context);

// Can Adam create a Project?
const canCreateProject = greenGardensInitiative.getPermission('addProject');
// returns true b/c Adam is member of the CityX Community Group

// Create project in initiative context
// this adds the typekeyword `initiative:<greenGardensId>`, "associating" it to the initiative
// it's not automatically shared to any groups b/c the shareTo array is empty
const pineStGardenProject = await greenGardensInitiative.create("project", {
  title: "Pine St Garden",
  geometry: {...},
  shareTo: [],
  // capabilities is still in the
  capabilities: ["Events", "Discussions"]
});


// alternatively we could do
pineStGardenProject.update({capabilities: ["Events", "Discussions"] }) =;
await pineStGardenProject.save();

// Later when Adam creates a group, he can share this project to it via
await pineStGardenProject.shareToGroup("3ef", {notify: true});
// which could also send notifications

// Create Event
// this checks that the project has `Event` capability, and user has permissions
const meetup = await pineStGardenProject.create("event",{
  title: "Pine St Garden Planning Meetup",
  ...
});

// Create Discussion
// this checks that the project has `Discussion` capability, and user has permissions
const post = await pineStGardenProject.create("post", {
  title: "How can we make this Garden useful to our neighborhood?",
  body: "...",
  discussion: "hub://item/<pineStGardenProjectPojo.id>"
});

```
