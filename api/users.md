# User endpoints

This set of endpoints is for managing users in the new CloudTrax user system.

functionality | method | endpoint
--- | --- | ---
[Edit a user](#put-user-id) | PUT | `/user/<id>`
[Disable a user](#put-user-id-disable) | PUT | `/user/<id>/disable`
[Enable a user](#put-user-id-enable) | PUT | `/user/<id>/enable`
[Delete a user](#delete-user-id) | DELETE | `/user/<id>`
[Get a user](#get-user-id) | GET | `/user/<id>`
[Create a user API key](#post-user-id-key) | POST | `/user/<id>/key`
[Create a user API key](#post-user-id-key) | POST | `/user/key`
[List user API keys](#get-user-id-key-list) | GET | `/user/<id>/key/list`
[Delete a user API key](#delete-user-id-key-id) | DELETE | `/user/<id>/key/<id>`
[Delete a user API key](#delete-user-id-key-id) | DELETE | `/user/key/<id>`
[Create a user](#post-user) | POST | `/user`
[List users](#get-user-list) | GET | `/user/list`
[Edit a user password](#put-user-id-password) | PUT | `/user/<id>/password`
[Create a user password reset token](#post-user-id-password_set) | POST | `/user/<id>/password_set`
[Get a user network permission](#get-user-id-network-id) | GET | `/user/<id>/network/<id>`
[Get a user account permission](#get-user-id-account) | GET | `/user/<id>/account`
[Get a user service agreement status](#get-user-id-service_agreement) | GET | `/user/<id>/service_agreement`
[Edit a user service agreement status](#put-user-id-service_agreement) | PUT | `/user/<id>/service_agreement`

<a name="put-user-id"></a>
### Edit a user
`PUT /user/<id>`

Edit details about a user.

##### Account Permissions
Account Admin, Group Manager

##### Resource Permissions
N/A

##### Example request

````
PUT https://api-v2.cloudtrax.com/user/123
````

```` json
{
	"name":"foo",
	"notes":"district manager. watch out for him."
}
````

##### Example response

````
HTTP 200
````

```` json
{}
````

#### Error codes

30002
20000
20038

<a name="put-user-id-disable"></a>
### Disable a user
`PUT /user/<id>/disable`

Disable a user. User will not be able to login or make any requests to the API after this endpoint has been successfully called. User information and permissions will be retained, but API keys will be removed.

##### Account Permissions
Account Admin, Group Manager

##### Resource Permissions
N/A

##### Example request

````
PUT https://api-v2.cloudtrax.com/user/123/disable
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{}
````

#### Error codes

30002
20038
20029

<a name="put-user-id-enable"></a>
### Enable a user
`PUT /user/<id>/enable`

Enable a user. User will be able to login and make requests to the API after this endpoint has been successfully called. User information and permissions will be restored, but new API keys will have to be generated.

##### Account Permissions
Account Admin, Group Manager

##### Resource Permissions
N/A

##### Example request

````
PUT https://api-v2.cloudtrax.com/user/123/enable
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{}
````

#### Error codes

30002
20038
20029

<a name="delete-user-id"></a>
### Delete a user
`DELETE /user/<id>`

Delete a user. All user information (including permissions and API keys) will be permanently removed.

##### Account Permissions
Account Admin, Group Manager

##### Resource Permissions
N/A

##### Example request

````
DELETE https://api-v2.cloudtrax.com/user/123
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{}
````

#### Error codes

30002
20004
20038
20029

<a name="get-user-id"></a>
### Get a user
`GET /user/<id>`

Get a user's information. This is information that is likely only of interest to Account Admins and Group Managers and not Network Users. For example, "notes" about a user may not be something administrators want to share. Or, "permissions" are only relevant to administrators, because a Network User cannot change their own.

##### Account Permissions
Account Admin, Group Manager

##### Resource Permissions
N/A

##### Example request

````
GET https://api-v2.cloudtrax.com/user/123
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{
	"email":"foo@bar.com",
	"name":"foo",
	"notes":"he is a bar",
	"role_id":2, /* account role ID */
	"account_id":123,
	"enabled":true,
	"verified":true,
	"permissions":{
		/* networks that were assigned explicitly and via network groups */
		"all_networks":[
			{"network_id":1, "role_id":3},
			{"network_id":2, "role_id":3},
			{"network_id":3, "role_id":3}
		],
		/* only specifically assigned networks */
		"assigned_networks":[
			{"network_id":3, "role_id":3}
		],
		/* only specifically assigned network groups */
		"assigned_networkgroups":[
			{"networkgroup_id":1, "role_id":3} /* contains network IDs 1 and 2 */
		],
	}
}
````

#### Error codes

30002
20004
20038

<a name="post-user-id-key"></a>
### Create a user API key
`POST /user/<id>/key`
`POST /user/key`

Create an API key for the specified user or the user making the request (if the `<id>` is not specified). API keys have identical API permissions to a logged in user, but do not require entering the user's email and password. They should still be kept secret, even though the user's password is never accessible.

Each user may have up to 10 keys generated via this endpoint at once.

The "key" property must be stored after creation, because it is stored securely and cannot be retrieved again.

##### Account Permissions
Account Admin, Group Manager, Network User

##### Resource Permissions
N/A

##### Example request

````
POST https://api-v2.cloudtrax.com/user/123/key
POST https://api-v2.cloudtrax.com/user/key
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{
	"user_key_id":123,
	"key":"abc",
	"secret":"def",
	"created":"1970-01-01T00:00:00Z"
}
````

#### Error codes

30002
20004
20038
20044

<a name="get-user-id-key-list"></a>
### List user API keys
`GET /user/<id>/key/list`

Get a list of API keys for the specified user that were generated via the [Create user API key](#post-user-id-key) endpoint.

The "key" is not returned, because it is stored securely. If you lose the "key", you must generated a new API key.

##### Account Permissions
Account Admin

##### Resource Permissions
N/A

##### Example request

````
GET https://api-v2.cloudtrax.com/user/123/key/list
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{
	"keys":[
		{
			"user_key_id":123,
			"secret":"def",
			"created":"1970-01-01T00:00:00Z"
		}
	]
}
````

#### Error codes

30002
20004
20038

<a name="delete-user-id-key-id"></a>
### Delete a user API key
`DELETE /user/<id>/key/<id>`
`DELETE /user/key/<id>`

Delete an API key (generated via the [Create user API key](#post-user-id-key) endpoint) for the specified user or the user making the request (if the `<id>` following `/user/` is not specified).

##### Account Permissions
Account Admin

##### Resource Permissions
N/A

##### Example request

````
DELETE https://api-v2.cloudtrax.com/user/123/key/456
DELETE https://api-v2.cloudtrax.com/user/key/456
````

```` json
{}
````

##### Example response

````
HTTP 200
````

```` json
{}
````

#### Error codes

30002
20004
20038
20043