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

# Resources

Each resource supports the same basic methods with a strong influence from REST. The hope is to provide a uniform API for any resource type. 

# Users

## Users

A User is always associated with a platform user. The user object will have a `slack_user`, `hipchat_user`, or `ms_user` object attached to it depending on the platform.


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

Key | Type | Description
--------- | ------- | -------
id | Integer | 
first_name | String |
last_name | String |
email | String |
is_deleted | Boolean | 
slack_user | Object | Associated Slack user.
ms_user | Object | Associated MS user.
hipchat_user | Object | Associated Hipchat user.


## Create a user

```javascript

const user = yield this.store.resources.Users.create({
	slack_user: { } // Platform slack user.
});
```

### Data Parameters

One platform user object must be passed into the `Users.create` method. The raw object from the platform will be translated into a Growbot user inserting an associated platform user object.

Parameter | Type | Description
--------- | ------- | -----------
slack_user | Object | Platform object for a Slack user (https://api.slack.com/types/user).
ms_user | Object | Platform object for a MS user.
hipchat_user | Object | Platform object for a Hipchat user.

## Get a user

```javascript

// Get a user by id
const userOne = yield this.store.resources.Users.get({
	id: 1
});

// Get a user by slack_user_id
const userSlack = yield this.store.resources.Users.get({
	slack_user_id: "U023BECGF"
});

// Get a user by ms_user_id
const userSlack = yield this.store.resources.Users.get({
	ms_user_id: "MSUSERID"
});

```

### Data Parameters

Similar to the create method, the get method accepts a variety of id types.

Parameter | Type | Description
--------- | ------- | -----------
id | Integer | The Growbot ID for a user.
slack_user_id | String | The Slack ID for a user.
ms_user_id | String | The MS ID for a user.
hipchat_user | String | The Hipchat id for a user.



# SlackUsers

## The Slack user object.


# MSUsers

# Kittens

## Get All Kittens

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

