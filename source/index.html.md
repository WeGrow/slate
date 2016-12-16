---
title: API Reference

language_tabs:
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Kudos

## The Kudo object.

### The Kudo object.

```javascript
// Associated objects are not entirely filled in.
{
	"id": 1,
	"created_at": "Thu Dec 15 2016 14:43:29 GMT-0800 (PST)",
	"updated_at": "Thu Dec 15 2016 14:43:29 GMT-0800 (PST)",
	"body": "Great job for stuff!",
	"team": {
		"id": 1,
		"name": "Colombia Records"
	},
	"channel": {
		id: 1,
		"name": "#kudos"
	},
	"sender": {
		"id": 2,
		"name": "J"
	},
	"receivers": [
		{
			"id": 1,
			"name": "V"
		}
	],
	"meta": {
	}
}

```

Key | Type | Description
--------- | ------- | -------
id | Integer | 
created_at | Date |
updated_at | Date |
body | String | Text content of the kudos.
team | Team | Associated team object.
channel | Channel | Associated channel object.
sender | User | User who sent the kudos.
receivers | Array | List of kudos receivers.
meta | Object | Meta data properties.


## Create a kudo.

```javascript

const { User, Team, Kudo, Channel } = store.resources;

// slack event received from slack
const slackEvent = { /* ... */ }

// get constituent parts that are required to create a kudos
// in reality, various info will be pulled out of the slack event
const msgBody = 'props to @slack_user_joe and @slack_user_jeremy for great work!'

// can use:
// `slack_user_id`
// `hipchat_user_id`
// `msteams_user_id`
const sender = yield User.get({ slack_user_id: 'slack_user_abel' });
if (!sender) {
    sender = yield User.create({ slack_user_id: 'slack_user_abel' });
}

const receivers = [
    yield User.get({ slack_user_id: 'slack_user_joe' }),
    yield User.get({ slack_user_id: 'slack_user_jeremy' }),
];

// can use:
// `slack_channel_id`
// `hipchat_channel_id`
// `msteams_channel_id`
const channel = yield Channel.get({ slack_channel_id: '2j3dkak3' })

const kudo = Kudo.create({
    body: msgBody,
    sender: sender,
    receivers: receivers,
    channel: channel,
    meta: {
        slackEvent: slackEvent
    }
});
```

### Data Parameters

A Kudo is created from a gbEvent. Nothing in the kudo should be platform specific; however, it may be necessary to connect platform messages to Growbot models. For example, when a user deletes a Slack message, their kudo is deleted. The reverse is also true, if a Kudo is removed from the web the corresponding Slack messages must be removed. To support this, we may have to use the `meta` property for the model.


Parameter | Type | Description
--------- | ------- | -----------
body | String | Text content of the kudo.
sender | User | The user giving the kudo.
receivers | Array<User> | List of users who are receiving the kudo.
channel | Channel | Channel the kudo is given in.
meta | Object | Arbitrary properties.


# Reactions

## The Reaction object.

### The Reaction object.

```javascript
{
	"id": 1,
	"created_at": "Thu Dec 15 2016 14:43:29 GMT-0800 (PST)",
	"updated_at": "Thu Dec 15 2016 14:43:29 GMT-0800 (PST)",
	"name": "raised_hands",
	"user": {
		"id": 1,
		"name": "sidhesh"
	},
	"kudo": {
		"id": 1,
		"body": "Great job @jeremy_slack"
	},
	"meta": {
		slackEvent: {}
	}
}
```

Parameter | Type | Description
--- | --- | ---
id | Integer |
created_at | Date |
updated_at | Date |
name | String | Reaction name.
user | User | The user giving the reaction.
kudo | Kudo | Associated kudo object.

## Create a reaction.

```javascript
const {User, SlackMessage, Kudo, Reaction, Channel} = store.resources;

// Incoming slack event for reaction_added.
const slackEvent = { /* ... */ };

// Originates from the Slack event, simplified here.
const user = yield User.get({
	slack_user_id: 'slack_user_sid'
});
const name = "raised_hands";

// Lookup the slack message to retrieve the kudo id.
const slackMessage = yield SlackMessage.get({
	ts: slackEvent.item.ts,
	channel: slackEvent.item.channel
});

const kudo = yield Kudo.get({
	id: slackMessage.meta.kudo_id
});

const reaction = yield Reaction.create({
	user: user,
	name: slackEvent.reaction,
	kudo: kudo
});
```
### Data Parameters

Reactions are added to Kudos by users within channels. It's essentially a like button for Kudos. In the Slack world, this is very rich since emoji's are used as Reactions.


Parameter | Type | Description
--------- | ------- | -----------
name | String | The name of the reaction.
user | User | The user that added the reaction.
kudo | Kudo | The kudo the reaction was added to.

# Teams

## The Team object.

Every user is connected to one team. A team also provides a few high-level properties that describe the team. A team has one originating platform (Slack, MS Teams, or Hipchat).

### The Team object.

> Slack Team example.

```json
{
	"id": 1,
	"is_enabled": true,
	"name": "Colombia Records",
	"domain": "columbia-records",
	"icon_url": "https://",
	"subscription": {
		"plan_id": 1,
		"is_premium": true
	},
	"slack_team": {
		"id": 1,
		"name": "Columbia Records",
		"slack_team_id": "TABCDEF",
		"domain": "columbia-records"
	}
}
```

Parameter | Type | Description
--------- | ------- | -------
id | Integer | 
is_enabled | Boolean | Enabled state for the team.
name | String | The Team name (defaults to the Platform team name).
domain | String | The subdomain used for web views.
icon_url | URL | The icon url for the team.
subscription | Subscripton | The team subscription if one exists.
slack_team | SlackTeam | Slack team resource if this team is a Slack team.
msteams_team | MSTeamsTeam | MSTeams resource if this team is a MSTeams team.
hipchat_team | HipchatTeam | HipchatTeam resource if this team is a Hipchat team.

## Create a Team

```javascript
const team = yield store.resources.Teams.create({
	slack_team: { } // Platform slack team.
});
```

### Parameters

The Platform object is used to create a Growbot team. The platform object will be translated into a Growbot Team.

Parameter | Type | Description
--------- | ---- | -------
slack_team | Object | Platform object for a Slack team. (As described here https://api.slack.com/methods/team.info).
msteams_team | Object | Platform object for a MSTeams team.
hipchat_team | Object | Platform object for a Hipchat team.

## Retrieve a Team

```javascript
// Retrieve a slack team.
const team = yield store.resources.Teams.get({
	slack_team_id: 'TABCDEF'
});

```

### Parameters

Parameter | Type | Description
--------- | ---- | -------
id | Integer | Growbot id for a team.
slack_team_id | String | Slack id for a team.
msteams_team_id | String | MSTeams id for a team.
hipchat_team_id | String | Hipchat id for a team.

## Update a Team

```javascript
const team = yield store.resources.Teams.update(id, {
	name: "A new name"
});
```

### Parameters

Parameter | Type | Description
--------- | ---- | -------
name | String | Updates the name of the team.
is_enabled | Boolean | Updates the enabled state of the team.
domain | String | Updates the domain for the team.

# SlackTeams
# MSTeamsTeams
# HipchatTeams

# Users

## The User object.

A User is always associated with a platform user. The user object will have a `slack_user`, `hipchat_user`, or `msteams_user` object attached to it depending on the platform.


### The User object.

> Slack User example.

```json
{
	"id": 1,
	"first_name": "Bob",
	"last_name": "Dylan",
	"email": "bob@malibu.com",
	"slack_user": {
		"id": "U023BECGF",
		"name": "bobby",
		"deleted": false
	}
}
```

Parameter | Type | Description
--------- | ------- | -------
id | Integer | 
first_name | String |
last_name | String |
email | String |
is_deleted | Boolean | 
slack_user | Object | Associated Slack user.
msteams_user | Object | Associated MS user.
hipchat_user | Object | Associated Hipchat user.


## Create a user

```javascript

const user = yield store.resources.Users.create({
	slack_user: { } // Platform slack user.
});
```

### Data Parameters

One platform user object must be passed into the `Users.create` method. The raw object from the platform will be translated into a Growbot user inserting an associated platform user object.

Parameter | Type | Description
--------- | ------- | -----------
slack_user | Object | Platform object for a Slack user (https://api.slack.com/types/user).
msteams_user | Object | Platform object for a MS user.
hipchat_user | Object | Platform object for a Hipchat user.

## Get a user

```javascript

// Get a user by id
const userOne = yield store.resources.Users.get({
	id: 1
});

// Get a user by slack_user_id
const userSlack = yield store.resources.Users.get({
	slack_user_id: "U023BECGF"
});

// Get a user by msteams_user_id
const userSlack = yield store.resources.Users.get({
	msteams_user_id: "MSUSERID"
});

```

### Data Parameters

Similar to the create method, the get method accepts a variety of id types.

Parameter | Type | Description
--------- | ------- | -----------
id | Integer | The Growbot ID for a user.
slack_user_id | String | The Slack ID for a user.
msteams_user_id | String | The MS ID for a user.
hipchat_user | String | The Hipchat id for a user.


# SlackUsers
# MSTeamsUsers
# HipchatUsers


# Resources

Each resource supports the same basic methods with a strong influence from REST. The hope is to provide a uniform API for any resource type.

Resources are accessed through the store via the `store.resoucres` reference. 

## Creating a resource

```javascript

const meme = yield store.resources.Memes.create({
	key: 'value'
});
```

## Getting a resource

```javascript
const meme = yield store.resources.Memes.get({
	id: 1
});
```

## Updating a resource

```javascript
const meme = yield store.resources.Memes.update({
	id: 1,
	val: 'hey'
});
```

## Deleting a resource

```javascript
const meme = yield store.resources.Memes.delete({
	id: 1
});
```

## Getting a list of resources.

```javascript
const memes = yield store.resources.Memes.list({
	funny: true
});
```
