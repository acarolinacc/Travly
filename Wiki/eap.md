# EAP: Architecture Specification and Prototype

**Connecting the World, One Adventure at a Time.**

Travly is a platform designed to unite passionate travelers, allowing them to authentically share their adventures, connect with fellow wanderers and explore the world together.

## A7: Web Resources Specification

> A7 provides an overview of the web resources to be implemented on the Travly platform, organized into modules. It also includes the permissions used in the modules to establish access conditions for resources. Web resources are specified using the OpenAPI standard.

### 1. Overview

> M01: Authentication	
Web resources associated with user authentication and registration, with features such as login/logout and registration.

> M02: Users
This module revolves around web resources designed for user-related interactions. Users can access various features such as viewing user information, navigating home and profile pages and editing profile details. This comprehensive set of resources enhances the overall user experience and interaction on the platform.

> M03: Administration
Within this module, web resources are aligned with the enforcement of terms of service. Administrative actions include blocking and unblocking users, banning users when necessary, deleting posts and comments that violate guidelines.

> M04: Posts
Resources associated with platform posts, including listing, creation, editing, deletion, and interactions such as likes and comments.

> M05: Groups
Resources associated with user groups, including creation, member management, group posts, and interactions within groups.

> M06: Search
This module encompasses web resources dedicated to search functionalities, allowing users to explore users and groups. Whether searching for specific content or users, this module ensures an efficient and tailored search experience.

> M07: Comments
Web resources in this module are focused on comment-related functionalities. Users can seamlessly create and delete comments, fostering an interactive and dynamic environment. Visualization features ensure a smooth and engaging comment viewing experience.

  

### 2. Permissions

> Defines the permissions used in the modules to establish access conditions for resources.

PUB: Public - Users without privileges.

USR: User - Authenticated users.

OWN: Owner - Users who are owners of the information.

ADM: Administrator - System administrators. 

### 3. OpenAPI Specification

```yaml
openapi: 3.0.0

info:
  version: '1.0'
  title: 'Travly Web API'
  description: 'Web Resources Specification (A7) for Travly'

servers:
- url: https://lbaw2334.lbaw.fe.up.pt
  description: Production server

externalDocs:
 description: Find more info here.
 url: https://git.fe.up.pt/lbaw/lbaw2324/lbaw2334/-/wikis/eap

tags:
 - name: 'M01: Authentication'
 - name: 'M02: Users'
 - name: 'M03: Administration'
 - name: 'M04: Posts'
 - name: 'M05: Groups'
 - name: 'M06: Search'
 - name: 'M07: Comments'

paths:

# ------------------------------
# Authentication Module
# ------------------------------

# User login

  /login:

    get:
      operationId: R101
      summary: 'R101: Login Form'
      description: 'Provide login form. Access: PUB'
      tags:
        - 'M01: Authentication'

      responses:
        '200':
          description: 'OK. Show log-in UI'

    post:
      operationId: R102
      summary: 'R102: Login Action'
      description: 'Processes the login form submission. Access: PUB'
      tags:
        - 'M01: Authentication'

      requestBody:
        required: true
        content:
          application/x-www-form-urllencoded:
            schema:
              properties:
                email:
                  type: string
                  format: email
                password:
                  type: string
                  format: password
              required:
                  - email
                  - password

      responses:
        '302':
          description: 'Redirect after processing the new user information.'
          headers:
            Location:
              schema:
                type: string
              examples:
                302Success:
                  description: 'OK. You are in. Redirect to homepage.'
                  value: '/home'
                302Failure:
                  description: 'You shall not pass. Redirect again to login form.'
                  value: '/login'

# ------------------------------

# User logout

  /logout:

    get:
      operationId: R103
      summary: 'R103 : Logout Action'
      description: 'Logout the current logged user. Access: USR, ADM'
      tags:
        - 'M01: Authentication'

      responses:
        '302':
          description: 'Redirect after processing logout.'
          headers:
            Location:
              schema:
                type: string
              examples:
                302Success:
                  description: 'Successful logout. Redirect to public feed.'
                  value: '/'


# ------------------------------

# User register

  /register:

    get:
      operationId: R104
      summary: 'R104 : Register Form'
      description: 'Register a new user. Access: PUB'
      tags:
        - 'M01: Authentication'

      responses:
        '200':
          description: 'Lets Sign-up.'

    post:
      operationId: R105
      summary: 'R105 : Register Action'
      description: 'Processes the new user registration form submission. Access: PUB'
      tags:
        - 'M01: Authentication'

      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                username:
                  type: string
                name:
                  type: string
                email:
                  type: string
                  format: email
                password:
                  type: string
                  format: password
                confirm_password:
                  type: string
                  format: password
              required:
                - username
                - name
                - email
                - password
                - confirm_password;

      responses:
        '302':
          description: 'Redirect after processing the new user information.'
          headers:
            Location:
              schema:
                type: string
              example:
                  302Success:
                    description: 'Successful registration. Redirect to home page.'
                    value: '/home'
                  302Failure:
                    description: 'Failed registration. Redirect again to register form.'
                    value: '/register'

# ------------------------------

# Recover password

  /recoverPassword:

    post:
      operationId: R107
      summary: 'R107 : Recover Password Action'
      description: 'Changes the current password after receiving the validation token. Access: VST'
      tags:
        - 'M01: Authentication'

      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                token:
                  type: string
                password:
                  type: string
                  format: password
                verify_password:
                  type: string
                  format: password
              required:
                - token
                - password
                - verify_password;

      responses:
        '200':
          description: 'Success. Your password has been changed successfully.'
        '404':
          description: 'Error. Invalid token.'

# ------------------------------
# Users Module
# ------------------------------

# View user profile

  /users/{id}:
    get:
      operationId: R201
      summary: 'R201: View user profile'
      description: 'Endpoint to view user profile information. Access: USR, ADM, VST, OWN'
      tags:
        - 'M02: Users'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
      responses:
        '200':
          description: 'User profile retrieved successfully.'
        '302':
          description: 'Redirect if user is not logged in or other user doesnt exists'
          headers:
            Location:
              schema:
                type: string
              example:
                302Failure:
                  description: 'Failure.'

# ------------------------------
# Update user profile

  /user/edit:
    get:
      operationId: R202
      summary: 'R202: Edit User Profile Page'
      description: 'Displays the edit profile page for the user. Access: OWN'
      tags:
        - 'M02: Users'
      responses:
        '200':
          description: 'OK. The edit profile page is now available.'
        '401':
          description: 'Unauthorized. You do not have permission to edit this profile.'
          headers:
            Location:
              schema:
                type: string
              examples:
                401Success:
                  description: 'Unauthorized. Redirect to the user profile.'
                  value: '/user/{id}'

    post:
      operationId: R203
      summary: 'R203: Save Edited User Profile'
      description: 'Processes and saves the changes made by the user. Access: USR'
      tags:
        - 'M02: Users'
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                name:
                  type: string
                username:
                  type: string
                email:
                  type: string
                  format: email
                description:
                  type: string
                  format: password
                password:
                  type: string
                  format: password
                confirm_password:
                  type: string
                  format: password
                image:
                  type: string
                  format: binary

              required:
                - name
                - username
                - email
                - description
      responses:
        '302':
          description: 'Redirect after processing the new user information.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful update. Redirect to the user profile page.'
                  value: '/user/{id}'
                302Failure:
                  description: 'Failed update. Redirect again to the edit profile page.'
                  value: '/user/edit'
        '400':
          description: 'Bad Request. The provided data is invalid.'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'

# ------------------------------
# View user's home page

  /home:
    get:
      operationId: R206
      summary: 'R206: View user home page'
      description: 'Displays the home page for an individual user. Access: User, Admin, Visitor, Owner'
      tags:
        - 'M02: Users'
      responses:
        '200':
          description: 'OK. Display user home page.'
        '302':
          description: 'Redirect if user is not logged in.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Failure:
                  description: 'Failure'

# ------------------------------
# Delete user profile

  /users/{id}/delete:
    delete:
      operationId: R207
      summary: 'R207: Delete User Profile'
      description: 'Endpoint to delete a user profile. Access: OWN, ADM'
      tags:
        - 'M02: Users'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
      responses:
        '302':
          description: 'Redirect after deleting user information.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful delete. Redirect to login page.'
                  value: '/login'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'
        '404':
          description: 'Not Found. The user does not exist.'
        '500':
          description: 'Internal Server Error. An unexpected error occurred.'

# ------------------------------

# ------------------------------
# Administration Module
# ------------------------------

# Admin Page

  /admin:
    get:
      operationId: R301
      summary: 'R301: View admin page'
      description: 'Shows the admin page. Access: ADM'
      tags:
        - 'M03: Administration'
      responses:
        '200':
          description: 'OK. Show the admin page'
        '302':
          description: 'Redirect if user is not logged in or does not have admin access'
          headers:
            Location:
              schema:
                type: string
              example:
                302Failure:
                  description: 'Failure. Redirect to login page.'
                  value: '/login'
        '403':
          description: 'Forbidden. You do not have permission to access this page.'
          headers:
            Location:
              schema:
                type: string
              example:
                403Failure:
                  description: 'Forbidden. Redirect to home page.'
                  value: '/home'


# ------------------------------

# Block a user from logging in action
  /admin/user/block:
    post:
      operationId: R302
      summary: 'R302: Block a user from logging in action'
      description: 'Blocks a user from logging in. Access: ADM'
      tags:
        - 'M03: Admin'
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                user_id:
                  type: integer
              required:
                - user_id
      responses:
        '302':
          description: 'Redirect after processing block action.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful block action. Redirect to admin page.'
                  value: '/admin'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'
        '404':
          description: 'Not Found. The user does not exist.'

# Unblocking a user from logging in action
  /admin/user/unblock:
    post:
      operationId: R303
      summary: 'R303: Unblocking a user from logging in action'
      description: 'Unblocks a user from logging in. Access: ADM'
      tags:
        - 'M03: Admin'
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                user_id:
                  type: integer
              required:
                - user_id
      responses:
        '302':
          description: 'Redirect after processing unblock action.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful unblock action. Redirect to admin page.'
                  value: '/admin'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'
        '404':
          description: 'Not Found. The user does not exist.'

# ------------------------------
# Ban a user action
  /admin/user/ban:
    post:
      operationId: R304
      summary: 'R304: Ban a user action'
      description: 'Bans a user. Access: ADM'
      tags:
        - 'M03: Admin'
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                user_id:
                  type: integer
              required:
                - user_id
      responses:
        '302':
          description: 'Redirect after processing ban action.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful ban action. Redirect to admin page.'
                  value: '/admin'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'
        '404':
          description: 'Not Found. The user does not exist.'

# ------------------------------
# Delete post or comment

  /admin/delete/post/{postId}:
    delete:
      operationId: R305
      summary: 'R305: Delete Post'
      description: 'Endpoint to delete a post. Access: ADM, USR'
      tags:
        - 'M03: Administration'
      parameters:
        - in: path
          name: postId
          schema:
            type: integer
          required: true
      responses:
        '204':
          description: 'Post deleted successfully.'
        '401':
          description: 'Unauthorized. You cannot deleted this post.'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'
        '404':
          description: 'Not Found. The post does not exist.'

  /admin/delete/comment/{commentId}:
    delete:
      operationId: R306
      summary: 'R306: Delete Comment'
      description: 'Endpoint to delete a comment. Access: ADM, USR'
      tags:
        - 'M03: Administration'
      parameters:
        - in: path
          name: commentId
          schema:
            type: integer
          required: true
      responses:
        '204':
          description: 'Comment deleted successfully.'
        '401':
          description: 'Unauthorized. You cannot deleted this comment.'
        '403':
          description: 'Forbidden. You do not have permission to perform this action.'
        '404':
          description: 'Not Found. The comment does not exist.'

# ------------------------------
# Posts Module
# ------------------------------

# List posts

  /posts:
    get:
      operationId: R401
      summary: 'R401: List Posts'
      description: 'Lists all posts. Access: USR, ADM'
      tags:
        - 'M04: Posts'
      parameters:
        - in: query
          name: id
          description: 'Filter posts by user ID.'
          schema:
            type: integer
          required: false
      responses:
        '200':
          description: 'OK. Display the list of posts.'
        '302':
          description: 'Redirect if user is not logged in.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Failure:
                  description: 'Failure. Redirect to login.'
                  value: '/login'
        '403':
          description: 'Forbidden. You do not have permission to access this page.'
          headers:
            Location:
              schema:
                type: string
              example:
                403Failure:
                  description: 'Forbidden. Redirect to home page.'
                  value: '/home'


# ------------------------------
# Create a post

  /posts/create:
    post:
      operationId: R402
      summary: 'R402: Create Post'
      description: 'Endpoint to create a new post. Access: USR'
      tags:
        - 'M04: Posts'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                  description: 'Post content.'
                media:
                  type: string
                  format: binary
                id:
                  type: integer
                  description: 'ID of the user creating the post.'
              required:
                - content
      responses:
        '201':
          description: 'Post created successfully.'
          content:
            application/json:
              example:
                message: 'Post created successfully.'
                post_id: 123
                link: '/posts/123'
        '401':
          description: 'Unauthorized. User not allowed to create a post.'
        '400':
          description: 'Bad Request. Invalid or missing parameters.'

# ------------------------------
# Edit a post

  /posts/edit/{postId}:
    put:
      operationId: R403
      summary: 'R403: Edit an existing post page.'
      description: 'Endpoint to edit an existing post.'
      tags:
        - 'M04: Posts'
      parameters:
        - in: path
          name: postId
          schema:
            type: integer
          required: true
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                media:
                  type: string
                  format: binary
              required:
                - content
      responses:
        '200':
          description: 'Ok. You can now edit. Show edit post UI.'
        '401':
          description: 'Unauthorized. You cannot edit this post.'
          headers:
            Location:
              schema:
                type: string
              examples:
                401Success:
                  description: 'Unauthorized. Redirect to post view.'
                  value: '/posts/{id}'
        '404':
          description: 'Not Found. The specified post does not exist.'

# ------------------------------
# Delete a post

  /posts/delete/{postId}:
    delete:
      operationId: R404
      summary: 'R404: Delete Post'
      description: 'Endpoint to delete a post.'
      tags:
        - 'M04: Posts'
      parameters:
        - in: path
          name: postId
          schema:
            type: integer
          required: true
      responses:
        '302':
          description: 'Redirect after processing the post deletion.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful deletion. Redirect to posts feed.'
                  value: '/posts'
                302Failure:
                  description: 'Failed deletion. Redirect again to post view.'
                  value: '/posts/{id}'
        '401':
          description: 'Unauthorized. You cannot delete this post.'
        '404':
          description: 'Not Found. The specified post does not exist.'

# ------------------------------
# Like a post

  /posts/like/{postId}:
    post:
      operationId: R405
      summary: 'R405: Like Post'
      description: 'Endpoint to like a post.'
      tags:
        - 'M04: Posts'
      parameters:
        - in: path
          name: postId
          schema:
            type: integer
          required: true
      responses:
        '302':
          description: 'Redirect after processing the post like.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful like. Redirect to posts feed.'
                  value: '/posts'
                302Failure:
                  description: 'Failed like. Redirect again to post view.'
                  value: '/posts/{id}'
        '401':
          description: 'Unauthorized. You cannot like this post.'
        '404':
          description: 'Not Found. The specified post does not exist.'

# ------------------------------
# Comment on a post

  /posts/comment/{postId}:
    post:
      operationId: R406
      summary: 'R406: Comment on Post'
      description: 'Endpoint to add a comment on a post.'
      tags:
        - 'M04: Posts'
      parameters:
        - in: path
          name: postId
          schema:
            type: integer
          required: true
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                content:
                  type: string
                  description: 'The content of the comment.'
                  example: 'Great post!'
              required:
                - content
      responses:
        '302':
          description: 'Redirect after processing the post comment.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Success:
                  description: 'Successful comment. Redirect to post view.'
                  value: '/posts/{id}'
                302Failure:
                  description: 'Failed comment. Redirect again to post view.'
                  value: '/posts/{id}'
        '401':
          description: 'Unauthorized. You cannot comment on this post.'
        '404':
          description: 'Not Found. The specified post does not exist.'

# ------------------------------
# Groups Module
# ------------------------------

# View Group Page

  /group/{id}:
    get:
      operationId: R501
      summary: 'R501: View Group Page'
      description: 'Show the group page for an individual user. Access: USR, ADM, VST, OWN'
      tags:
        - 'M05: Groups'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
          description: 'ID of the group to view.'
      responses:
        '200':
          description: 'OK. Show the group page.'
        '302':
          description: 'Redirect if the user is not logged in or the group doesn't exist.'
          headers:
            Location:
              schema:
                type: string
              example:
                302Failure:
                  description: 'Failure. Redirect to a specific location.'
        '403':
          description: 'Forbidden. You do not have permission to access this group.'
          headers:
            Location:
              schema:
                type: string
              example:
                403Failure:
                  description: 'Forbidden. Redirect to home page.'
                  value: '/home'

# ------------------------------
# Create a group

  /group/create:
    post:
      operationId: R502
      summary: 'R502: Create Group'
      description: 'Create a new group. Access: USR'
      tags:
        - 'M05: Groups'
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                name:
                  type: string
                  description: 'Name of the group.'
                description:
                  type: string
                  description: 'Description of the group.'
              required:
                - name
                - description
      responses:
        '201':
          description: 'Created. The group has been successfully created.'
          headers:
            Location:
              schema:
                type: string
              example:
                201Success:
                  description: 'Successful creation. Redirect to the new group page.'
                  value: '/groups/{id}'
        '400':
          description: 'Bad Request. Invalid parameters provided.'
        '403':
          description: 'Forbidden. You do not have permission to create a group.'

# ------------------------------
# Edit a group

  /group/edit/{id}:
    put:
      operationId: R503
      summary: 'R503: Edit Group'
      description: 'Edit an existing group. Access: USR, ADM, OWN'
      tags:
        - 'M05: Groups'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
          description: 'ID of the group to edit.'
      requestBody:
        required: true
        content:
          application/x-www-form-urlencoded:
            schema:
              type: object
              properties:
                name:
                  type: string
                  description: 'New name of the group.'
                description:
                  type: string
                  description: 'New description of the group.'
              required:
                - name
                - description
      responses:
        '200':
          description: 'OK. The group has been successfully edited.'
        '400':
          description: 'Bad Request. Invalid parameters provided.'
        '403':
          description: 'Forbidden. You do not have permission to edit this group.'
        '404':
          description: 'Not Found. The specified group was not found.'

# ------------------------------
# Delete a group

  /group/delete/{id}:
    delete:
      operationId: R504
      summary: 'R504: Delete Group'
      description: 'Delete an existing group. Access: USR, ADM, OWN'
      tags:
        - 'M05: Groups'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
          description: 'ID of the group to delete.'
      responses:
        '204':
          description: 'Group deleted successfully.'
        '401':
          description: 'Unauthorized. You cannot delete this group.'
        '403':
          description: 'Forbidden. You do not have permission to delete this group.'
        '404':
          description: 'Not Found. The specified group was not found.'

# ------------------------------
# Join a group

  /group/join/{id}:
    post:
      operationId: R505
      summary: 'R505: Join Group'
      description: 'Join an existing group. Access: USR'
      tags:
        - 'M05: Groups'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
          description: 'ID of the group to join.'
      responses:
        '200':
          description: 'OK. Successfully joined the group.'
        '403':
          description: 'Forbidden. You do not have permission to join this group.'
        '404':
          description: 'Not Found. The specified group was not found.'
        '409':
          description: 'Conflict. You are already a member of this group.'

# ------------------------------
# Leave a group

  /group/leave/{id}:
    post:
      operationId: R506
      summary: 'R506: Leave Group'
      description: 'Leave an existing group. Access: USR'
      tags:
        - 'M05: Groups'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
          description: 'ID of the group to leave.'
      responses:
        '200':
          description: 'OK. Successfully left the group.'
        '403':
          description: 'Forbidden. You do not have permission to leave this group.'
        '404':
          description: 'Not Found. The specified group was not found.'
        '409':
          description: 'Conflict. You are not a member of this group.'

# ------------------------------

# ------------------------------
# Search Module
# ------------------------------

# Search Users

  /search/users:
    get:
      operationId: R601
      summary: 'R601: Search Users'
      description: 'Search for users based on specified criteria. Access: USR'
      tags:
        - 'M06: Search'
      parameters:
        - in: query
          name: query
          schema:
            type: string
          required: true
          description: 'Search query for users.'
      responses:
        '200':
          description: 'OK. Successfully retrieved search results.'
          content:
            application/json:
              example:
                users:
                  - id: 1
                    username: 'user1'
                    profile_url: '/users/1'
                  - id: 2
                    username: 'user2'
                    profile_url: '/users/2'
        '400':
          description: 'Bad Request. Invalid search query.'
        '403':
          description: 'Forbidden. You do not have permission to search for users.'

# ------------------------------
# Search Groups

  /search/groups:
    get:
      operationId: R602
      summary: 'R602: Search Groups'
      description: 'Search for groups based on specified criteria. Access: USR'
      tags:
        - 'M06: Search'
      parameters:
        - in: query
          name: query
          schema:
            type: string
          required: true
          description: 'Search query for groups.'
      responses:
        '200':
          description: 'OK. Successfully retrieved search results.'
          content:
            application/json:
              example:
                groups:
                  - id: 1
                    name: 'Group 1'
                    group_url: '/group/1'
                  - id: 2
                    name: 'Group 2'
                    group_url: '/group/2'
        '400':
          description: 'Bad Request. Invalid search query.'
        '403':
          description: 'Forbidden. You do not have permission to search for groups.'

# ------------------------------

# ------------------------------
# Comments Module
# ------------------------------

# Create Comment

  /comments:
    post:
      operationId: R701
      summary: 'R701: Create Comment'
      description: 'Create a new comment on a post. Access: USR'
      tags:
        - 'M07: Comments'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                postID:
                  type: integer
                  description: 'ID of the post to which the comment is added.'
                content:
                  type: string
                  description: 'Content of the comment.'
                  example: 'This is a comment.'
              required:
                - postID
                - content
      responses:
        '201':
          description: 'Created. Comment successfully added.'
          content:
            application/json:
              example:
                id: 456
                postId: 123
                content: 'This is a comment.'
        '400':
          description: 'Bad Request. Invalid data provided in the request body.'
        '401':
          description: 'Unauthorized. You do not have permission to create a comment.'
        '403':
          description: 'Forbidden. You do not have access to add a comment to this post.'

# ------------------------------
# Delete Comment
  
  /comments/{id}:
    delete:
      operationId: R702
      summary: 'R702: Delete Comment'
      description: 'Delete an existing comment. Access: USR'
      tags:
        - 'M07: Comments'
      parameters:
        - in: path
          name: id
          schema:
            type: integer
          required: true
          description: 'ID of the comment to delete.'
          example: 456
      responses:
        '204':
          description: 'No Content. Comment successfully deleted.'
        '401':
          description: 'Unauthorized. You do not have permission to delete this comment.'
        '403':
          description: 'Forbidden. You do not have access to delete this comment.'
        '404':
          description: 'Not Found. Comment not found.'
```

> Link to the `a7_openapi.yaml` file in the group's repository.


---


## A8: Vertical prototype

> The purpose of this artifact is to validate the presented architecture and provide us with foundational insights into the technologies utilized in the project. Following recommendations, the implementation is grounded in the LBAW Framework code, encompassing efforts across all layers of the solution's architecture.

### 1. Implemented Features

#### 1.1. Implemented User Stories

> Identify the user stories that were implemented in the prototype.  

| User Story reference | Name                   | Priority                   | Description                   |
| -------------------- | ---------------------- | -------------------------- | ----------------------------- |
| US01                 | See Home | High | As a User, I want to access the Home page, so that I can see a brief presentation of the website. |
| US02                 | See About | High | As a User, I want to access the About page, so that I can see a brief description of the website, its functionalities and its creators. |
| US03                 | See Help | High | As a User, I want to access the Help page, so that I can get help or clarify any doubt. |
| US04                 | See Privacy Policy | High | As a User, I would like to access the Privacy Policy page, so that I can review the terms and guidelines, gaining a better understanding of how my information is handled and ensuring transparency in data privacy practices. |
| US05                 | See FAQ | High | As a User, I would like to access the FAQ page, so that I can view common questions and find answers to clarify any doubts I may have. |
| US06                 | Sign-up | High | As a Visitor, I want to sign up for the system, so that I can enable myself to authenticate and access its features. |
| US07                 | Sign-in | High | As a Visitor, I want to authenticate into the system, so that I can access more features. |
| US12                 | See Profile | High | As a Registered User, I want to acess my Profile page, so I can see how my feed looks. |
| US21                 | Private Profile | High | As a Registered User, I want to keep my account private, so that I can decide who can follow me. |
| US23                 | Countries Visited | High | As a Registered User, I want to show the countries I've visited on my profile, so that I can provide a visual record for myself and visitors to see. |
| US31                 | Post Picture and/or Video | High | As an Author, I want to access my gallery, so that I can post one or more pictures/videos from it.|
| US32                 | Post Text | High | As a Author, I want to post text, so that I can write and share my thoughts.|

#### 1.2. Implemented Web Resources

> Identify the web resources that were implemented in the prototype.  

> Module M01: Authentication  

| Web Resource Reference | URL                            |
| ---------------------- | ------------------------------ |
| R101: Login Form  | GET /login |
| R102: Login Action  | POST /login |
| R103: Logout Action  | POST /logout |
| R104: Register Form  | GET /register |
| R105: Register Action  | POST /register |

> Module M02: Users  

| Web Resource Reference | URL                            |
| ---------------------- | ------------------------------ |
| R201: View user profile  | GET /users/{id} |
| R202: Edit User Profile Page  | GET /user/edit |

> Module M04: Posts  

| Web Resource Reference | URL                            |
| ---------------------- | ------------------------------ |
| R401: List Posts | GET /posts |
| R402: Create Post | POST /posts/create |

### 2. Prototype

> URL of the prototype plus user credentials necessary to test all features. 

The prototype is available at 
http://127.0.0.1:8000/login

Credentials:

Regular user:
* email: andr28@gmail.com
* pass:  my_vip_account 

> Link to the prototype source code in the group's git repository. 
https://git.fe.up.pt/lbaw/lbaw2324/lbaw2334

---


## Revision history

Changes made to the first submission:
1. Item 1: EAP update

-Utilized the OpenAPI standard for web resource specification.

-Organized web resources into modules, detailing functionalities and access conditions.

-Defined key modules and established a clear structure for resource permissions.

-Implemented the architecture using the LBAW Framework code.

-Developed features based on user stories, prioritizing high-priority tasks.

-Identified and implemented web resources for authentication (Module M01) and users (Module M02).

-Successfully implemented high-priority user stories, including accessing the Home page, signing up, signing in, viewing the profile, keeping the profile private, and showcasing visited countries.

-Implemented web resources for authentication (Module M01), including login and registration actions.

-Developed web resources for users (Module M02), enabling viewing user profiles and editing profile details.

-Developed web resources for posts (Module M04), allowing to list posts and create posts (some details to adjust).


***
GROUP2334, 23/11/2023
 
* Ana Carolina Coutinho, up202108685@fe.up.pt (editor)
* Inês Miranda, up202108775@fe.up.pt
* André Rodrigues, up202108721@fe.up.pt
* Rui Carvalho, up202108807@fe.up.pt