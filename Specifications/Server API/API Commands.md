Commands:

* `vocabulary`
    * Request
    * Response
* `reconcile`
    * Request
        * `constellation`: The Constellation to reconcile 
    * Response
* `start_session`
    * Request
    * Response
* `end_session`
    * Request
    * Response
* `user_information`
    * Request
    * Response
        * `user`: User information (User object) 
        * `groups`: List of Groups this user is in
        * `editing`: List of Constellations the user has checked out for editing
        * `editing_lock`: List of Constellations the user is currently editing (locked)
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `admin_users`
    * Request
    * Response
        * `users`: List of Users
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `edit_user`
    * Request
    * Response
        * `user`: User information (User object) 
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `update_user`
    * Request
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `group_information`
    * Request
    * Response
        * `group`: Group information (Group object)
        * `users`: List of Users
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `admin_groups`
    * Request
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `edit_group`
    * Request
    * Response
        * `group`: Group information (Group object)
        * `users`: List of Users
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `update_group`
    * Request
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `admin_institutions`
    * Request
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `insert_constellation`
    * Request
        * `constellation`: The Constellation to insert 
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `update_constellation`
    * Request
        * `constellation`: The Constellation to update
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `unlock_constellation`
    * Request
        * `constellation`: The Constellation to unlock 
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `publish_constellation`
    * Request
        * `constellation`: The Constellation to publish
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `delete_constellation`
    * Request
        * `constellation`: The Constellation to delete
    * Response
        * `result`: Message indicating `success` or `failure` of the operation
        * `error`: Error message (if `result` = `failure`)
* `recently_published`
    * Request
    * Response
* `read`
    * Request
        * `constellation`: The Constellation to read
    * Response
        * `constellation`: The requested Constellation 
* `edit`
    * Request
        * `constellation`: The Constellation to edit
    * Response
        * `constellation`: The requested Constellation 

<!--
All Requests
        * `constellation`: The Constellation
All Responses
        * `constellation`: The requested constellation 
        * `editing`: List of Constellations the user has checked out for editing
        * `editing_lock`: List of Constellations the user is currently editing (locked)
        * `error`: Error message (if `result` = `failure`)
        * `group`: Group information (Group object)
        * `groups`: List of Groups
        * `group_update`: 
        * `result`: Message indicating `success` or `failure` of the operation
        * `results`: 
        * `term`: Term object searched for
        * `term`: 
        * `users`: List of Users
        * `user`: User information (User object) 
        * `user_update`: 
        * `user_update`: 
        * `user`: 
-->
