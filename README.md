# Holdsport API documentation
This readme describes the Holdsport REST API.

## Authentication
The API uses http basic authentication. If the username is demo and the password demo:
```bash
http://demo:demo@api.holdsport.dk/v1/teams
```
## Data format
JSON UTF-8 encoded strings.

## The API
### Teams
#### Getting the list of teams
Example
```bash
curl -u "demo:demo" -H "Accept: application/json" http://api.holdsport.dk/v1/teams
```

The result
```json
[
    {
        "id": 6237, 
        "name": "frenke", 
        "primary_color": "", 
        "secondary_color": ""
    }, 
...
    {
        "id": 7369, 
        "name": "Hurricanes", 
        "primary_color": "#ed2637", 
        "secondary_color": "#ffffff"
    }
] 
```

### Activities
#### Getting the list of activities
Example
```
curl -u "demo:demo" -H "Accept: application/json" http://api.holdsport.dk/v1/teams/6237/activities
````

The result
```json
[
    {
        "comment": "", 
        "endtime": "", 
        "id": 256592, 
        "name": "Tur til Tyskland", 
        "pickup_place": "", 
        "pickup_time": "", 
        "place": "", 
        "starttime": "2012-01-13T01:00:00+01:00", 
        "status": "tilmeldt",
        "action_method": "PUT", 
        "action_path": "/v1/activities/256592/activities_users/2363897", 
        "actions": []
    }, 
    {
        "comment": "", 
        "endtime": "", 
        "id": 256593, 
        "name": "Sverigestur", 
        "pickup_place": "", 
        "pickup_time": "", 
        "place": "", 
        "starttime": "2012-01-27T01:00:00+01:00", 
        "status": "ej tilkendegivet",
        "action_method": "POST", 
        "action_path": "/v1/activities/256593/activities_users", 
        "actions": [
            {
                "activities_user": {
                    "joined_status": 1, 
                    "name": "tilmeld", 
                    "picked": 1
                }
            }
        ]
    }
]
```
The _status_ value denotes the users current status with respect to the activity.

The _actions_ arrays denotes the possible actions that the user can take with respect to this activity.

_Pagination_ is done by adding a _page_ and _per_page_ parameter.

Example
```
curl -u "demo:demo" http://api.holdsport.dk/v1/teams/7585/activities?page=2&per_page=10
```
Furthermore, a date parameter can be added to return activities from this day and onwards.

Example
```
curl -u "demo:demo" http://api.holdsport.dk/v1/teams/7585/activities?date=2012-01-1
```
The default for these parameters are _page=1_, _per_page=20_, and _date = today_.

#### Change the user status (attending, unregistering, etc.)
The interesting part of the response is this
```json
{
        "status": "ej tilkendegivet",
        "action_method": "POST", 
        "action_path": "/v1/activities/256593/activities_users", 
        "actions": [
            {
                "activities_user": {
                    "joined_status": 1, 
                    "name": "tilmeld", 
                    "picked": 1
                }
            }
        ]
}
```
_status_ denotes the user's current status with the respect to the activity.
_actions_ is an array of the possible actions for the user. In the example one action is allowed (_tilmeld_ (attend)) and the user status is changed to _tilmeldt_ by performing an _action_method_ (POST in the example) request to the _action_path_ URL (/v1/activities/256593/activities_users in the example) with a json encoded version of
```json
{
  "activities_user": {
                    "joined_status": 1, 
                    "picked": 1
                }
}
```
Or speaking _curl_:
```
curl -H "Content-Type: application/json" -X POST -u "demo:demo" http://api.holdsport.dk/v1/activities/256593/activities_users -d '{"activities_user": {"joined_status": 1, "picked": 1}}'
```
If the request was successful a HTTP status 201 (Created) is returned.

If there was an error a HTTP status 422 (Unprocessable Entity) is returned.

#### Getting the list of attendees for an activity
Example
```
curl -u "demo:demo" -H "Accept: application/json" http://api.holdsport.dk/v1/activities/256593/activities_users
```

The result
```json
[
    {
        "id": 2363898, 
        "name": "Demo Demo", 
        "status": "tilmeldt", 
        "updated_at": "2011-12-21T14:03:42+01:00", 
        "user_id": 58856
    }
]
```

#### Adding a comment to an activity
Example
```
curl -H "Content-Type: application/json" -X POST -u "demo:demo" http://api.holdsport.dk/v1/activities/273260/comments -d '{"comment": {"body": "Testkommentar"}}'
```

### Getting the list of team members
Example
```
curl -u "demo:demo"  -H "Accept: application/json" http://api.holdsport.dk/v1/teams/7585/members
```

The result
```json
[
  { "address" : " ",
    "email" : "info@holdsport.dk",
    "id" : 78776,
    "mobile" : "12345678",
    "name" : "Demo Demo",
    "role" : 2
  },
  { "address" : "8000 Aarhus",
    "email" : "fran@juj.dk",
    "id" : 79413,
    "mobile" : "",
    "name" : "Kkk Kkkk"
    "role" : 1
  }
]
```
The _role_ field can have three possible values 1 for player, 2 for coach and 3 for assistant coach.

### Getting the user profile
Example
```
curl -u "demo:demo"  -H "Accept: application/json" http://api.holdsport.dk/v1/user
```

Result
```json
{
    "addresses": [
        {
            "city": "aarhus bj", 
            "email": "info@holdsport.dk", 
            "email_ex": "Hyl@h", 
            "mobile": "22775467", 
            "parents_name": "Jens Baggesen", 
            "postcode": "8000", 
            "street": "Dalga ", 
            "telephone": "80964"
        }, 
        {
            "city": "aarhus ", 
            "email": "info@holdsport.dk", 
            "email_ex": "Hyl@h ii. bi", 
            "mobile": "22775467", 
            "parents_name": "Jens Baggesen", 
            "postcode": "8000 b", 
            "street": "Dalga s", 
            "telephone": "80964666"
        }
    ], 
    "birthday": "1925-05-05", 
    "firstname": "Demo han", 
    "id": 99464, 
    "lastname": "Danser Ye"
}
```


### Updating the user profile
Example
```
curl -H "Content-Type: application/json" -X PUT -u "demo:demo" http://api.holdsport.dk/v1/user -d '{"user": {"street": "Sandageralle", "postcode": "8300", "city": "Odder", "firstname": "Demo", "lastname": "Demosen", "mobile": "88888888", "email": "test@holdsport.dk"}}'
