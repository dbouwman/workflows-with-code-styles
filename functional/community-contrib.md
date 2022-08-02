## Community Contributor Workflow, Functional Style

```js

// fetch green-gardens initiative
const greenGardensPOJO = initiativeManager.fetchInitiative('green-gardens', context.requestOptions);

// Can Adam create a Project?
const canCreateProject = getPermission(greenGardensPOJO, 'addProject', context.currentUser);
// returns true b/c Adam is member of the CityX Community Group

// create project in initiative context
// this adds the typekeyword `initiative:<greenGardensId>`, "associating" it to the initiative
// it's not automatically shared to any groups
const pineStGardenProjectPojo = await createProject({
  title: "Pine St Garden",
  geometry: {...},
  shareTo: [],
  capabilities: ["Events", "Discussions"]
  associateWith: {type: "initiative", id: greenGardensPOJO.id}
}, context.userRequestOptions);


// alternatively we could do
pineStGardenProjectPojo.capabilities = ["Events", "Discussions"];
await saveProject(pineStGardenProjectPojo, context.userRequestOptions);

// Later when Adam creates a group, he can share this project to it via
await shareToGroup(pineStGardenProjectPojo, "3ef", context.userRequestOptions);


// Create Event
// verify permission
const canCreateEvent = getPermission(pineStGardenProjectPojo, "addEvent", context.currentUser);
// returns true because Adam is the owner

const hasEventCapability = getCapability(pineStGardenProjectPojo, "Events");
// returns true b/c it has the capability
// this fn is on the edge of being worth it, but it's a good abstraction
// should we change the implementation

if (canCreateEvent && hasEventCapbility) {
  const meetup = await createEvent(pineStGardenProjectPojo, {
    title: "Pine St Garden Planning Meetup",
    ...
  },
  context.userRequestOptions);
}

// Create Discussion

// verify permission and capability
const canCreateDiscussion = getPermission(pineStGardenProjectPojo, "addDiscussion", context.currentUser);
const hasDiscussionCapability = getCapability(pineStGardenProjectPojo, "Discussions");

if (canCreateDiscussion && hasDiscussionCapability) {
  const post = await createPost(pineStGardenProjectPojo, {
    title: "How can we make this Garden useful to our neighborhood?",
    body: "...",
    discussion: "hub://item/<pineStGardenProjectPojo.id>"
  }, context.userRequestOptions);
}


```
