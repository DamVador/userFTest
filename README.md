
UserFrosting 0.1.2
===================

### By Alex Weissman

Copyright (c) 2014

Welcome to UserFrosting, a secure, modern user management system for web services and applications.  UserFrosting is based on the popular UserCake system, written in PHP.  UserFrosting improves on this system by adding a sleek, intuitive frontend interface based on HTML5 and Twitter Bootstrap.  We've also separated the backend PHP machinery that interacts with the database from the frontend code base.  The frontend and backend talk to each other via AJAX and JSON.

Change Log - v0.1.2 
-------------------
- Improved error and exception handling
- Added 404 error page
- Standardized JSON interface for backend scripts
- Front-end should now be able to catch virtually any backend error and take an appropriate action (instead of white screen of death)

Why UserFrosting?
-----------------
This project grew out of a need for a simple user management system for my tutoring business, [Bloomington Tutors](http://bloomingtontutors.com).  I wanted something that I could develop rapidly and easily customize for the needs of my business.  Since my [prior web development experience](http://alexanderweissman.com/completed-projects/) was in pure PHP, I decided to go with the PHP-based UserCake system.  Over time I modified and expanded the codebase, turning it into the UserFrosting project. 

Why not use Node/Drupal/Django/RoR/(insert favorite framework here)?
--------------------------------------------------------------------
I chose PHP because PHP is what I know from my prior experience as a web developer. Additionally, PHP remains extremely popular and well-supported.  I chose not to use a framework because I wanted something that I could understand easily and develop rapidly from an existing PHP codebase.

Developer Features
-----------------
- No need to learn a special framework!  The backend of UserFrosting is based on native PHP5, allowing for rapid development and deployment.
- Clean separation of backend and frontend code.  Easily interact with the backend via AJAX calls.
- Automated installation tool for initializing the database.
- Frontend built with jQuery and Twitter Bootstrap.  Javascript components for typical database CRUD operations provided with this distribution!

User Features
--------
UserFrosting offers all of the features of UserCake, plus several new ones:

- Account creation/deletion from the admin interface
- Admin can disable/enable individual accounts
- Admin can disable/enable new account registration
- Dropdown menus for easier account modifications
- Client-side data validation
- Default permissions for new accounts
- Table view for easily editing page permissions
           
Screenshots
-----------------
#### Login page
![Login page](/screenshots/login.png "Login page")
#### Admin dashboard (thanks to [Start Bootstrap](http://startbootstrap.com))
![Admin dashboard](/screenshots/dashboard.png "Admin dashboard")
#### User list page
![User list](/screenshots/users.png "User list page")
#### Create user dialog
![Create user](/screenshots/create_user.png "Create user dialog")
#### User details
![User details](/screenshots/user_details.png "User details page")
#### Site settings
![Site settings](/screenshots/site_settings.png "Site settings page")
#### Page management
![Site pages](/screenshots/site_pages.png "Page management")

Installation
--------------
To install, follow these instructions:

1. Create a database on your server / web hosting package. UserFrosting supports MySQLi and requires MySQL server version 4.1.3 or newer, as well as PHP 5.4 or later with PDO database connections enabled.
2. Open up models/db-settings.php and fill out the connection details for your database.
3. Run the UserFrosting installer at install/install.php. UserFrosting will attempt to build the database for you.
4. After the installer successfully completes, delete the install folder.
5. Register your admin account, which will by default be the first account that is registered.

Documentation
-------------

### Backend pages
These are pages which accept a POST or GET request, perform some action, and then return a response in the form of a JSON object.  Success, error, and warning messages are stored in the `$_SESSION['userAlerts']` object, and can be fetched via **user_alerts.php**.

- **activate_account.php**
  + Activates a newly registered user account, using the activation token that was emailed to the user upon account registration.
  + Default permission level: *public*
  + Request method: *POST*
  + Request parameters:
    * `token` : a user's account activation token 
    * `ajaxMode` : Specify whether to access in AJAX/JSON mode.  `"true"` or `"false"`. 
  + Response (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * `FORGOTPASS_INVALID_TOKEN` : Token not specified
    * `ACCOUNT_TOKEN_NOT_FOUND` : Invalid token
    * `SQL_ERROR` : Database error
  + Possible successes generated (stored in `$_SESSION['userAlerts']`): 
    * `ACCOUNT_ACTIVATION_COMPLETE` : Account successfully activated.


- **admin_activate_user.php**
  + Manually activates a newly registered user account, using the user's user id (`id` column in `uc_users`).
  + Default permission level: *admin only*
  + Request method: *POST*
  + Request parameters:
    * `user_id` : a user's user id.
    * `ajaxMode` : Specify whether to access in AJAX/JSON mode.  `"true"` or `"false"`. 
  + Response (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * `ACCOUNT_INVALID_USER_ID` : Invalid user id specified
    * `SQL_ERROR` : Database error
  + Possible successes generated (stored in `$_SESSION['userAlerts']`): 
    * `ACCOUNT_MANUALLY_ACTIVATED` : Manual account activation successful.


- **admin_load_permissions.php**
  + Load permissions for a specified user, using the user's user id (`id` column in `uc_users`).
  + Default permission level: *admin only*
  + Request method: *GET*
  + Request parameters:
    * `user_id` : a user's user id.
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Obviously set to 0.
  + Response on success (JSON):
    * A JSON object with permission id's (from `uc_permissions`) as keys, mapping to permission objects for each permission group that the user has access to.  Each permission object contains the following fields:
        - `id` : the unique id for this permission group.
        - `name` : the name of the permission group.
        - `is_default` : specify whether the permission group is automatically added when a new account is registered. (`"1"` or `"0"`).
        - `can_delete` : specify whether the permission group can be deleted via "delete_permission.php". (`"1"` or `"0"`).
        - `user_id` : the id of the queried user.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * User id not specified
    * Database error


- **create_permission.php**
  + Create a new permission group.
  + Default permission level: *admin only*
  + Request method: *POST*
  + Request parameters:
    * `new_permission` : the name of the new permission group.  Must be between 1 and 50 characters long.
    * `ajaxMode` : specify whether to access in AJAX/JSON mode.  `"true"` or `"false"`. 
  + Response (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * `PERMISSION_NAME_IN_USE` : Permission group name already in use.
    * `PERMISSION_CHAR_LIMIT` : Permission group name too short/long.
    * `SQL_ERROR` : Database error
    * `NO_DATA` : No request data was received by the script.
  + Possible successes generated (stored in `$_SESSION['userAlerts']`): 
    * `PERMISSION_CREATION_SUCCESSFUL` : Permission group successfully created.


- **create_user.php**
  + Create a new user.
  + Default permission level: *admin only*
  + Request method: *POST*
  + Request parameters:
    * `username` : the username for the new user.  Must be between 1 and 25 characters long, letters and numbers only (no whitespace).
    * `displayname` : the display name for the new user.  Must be between 1 and 50 characters long.
    * `title` : the title for the new user.  Must be between 1 and 150 characters long.
    * `email` : the email address for the new user.  Must be between 1 and 150 characters and a valid email format.
    * `password` : the password for the new user.  Must be between 8 and 50 characters.
    * `confirm_pass` : the password for the new user.  Must match the `password` field.
    * `add_permissions` : a comma-separated string of permission id's to be associated with the new user.
    * `ajaxMode` : specify whether to access in AJAX/JSON mode.  `"true"` or `"false"`. 
  + Response (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * `ACCOUNT_USER_CHAR_LIMIT` : `username` too short/long.
    * `ACCOUNT_USER_INVALID_CHARACTERS` : `username` must be letters and numbers only.
    * `ACCOUNT_DISPLAY_CHAR_LIMIT` : `displayname` too short/long.
    * `ACCOUNT_PASS_CHAR_LIMIT` : `password` too short/long.
    * `ACCOUNT_PASS_MISMATCH` : `password` and `confirm_pass` do not match.
    * `ACCOUNT_INVALID_EMAIL` : `email` is not a valid email address format.
    * `ACCOUNT_USERNAME_IN_USE` : `username` already in use.
    * `ACCOUNT_DISPLAYNAME_IN_USE` : `displayname` already in use.  TODO: relax this restriction?
    * `ACCOUNT_EMAIL_IN_USE` : `email` already in use.
    * `MAIL_ERROR` : Error sending activation email (if enabled).
    * `SQL_ERROR` : Database error
    * `NO_DATA` : No request data was received by the script.
  + Possible successes generated (stored in `$_SESSION['userAlerts']`): 
    * `ACCOUNT_CREATION_COMPLETE` : User account successfully created.
    * `ACCOUNT_PERMISSION_ADDED` : Permissions successfully added for new user.


- **delete_permission.php**
  + Delete a permission group, and all it's page and user associations.  Groups "User" and "Admin" cannot be deleted.  TODO: decide if a group can be deleted based on the `can_delete` field.
  + Default permission level: *admin only*
  + Request method: *POST*
  + Request parameters:
    * `permission_id` : the id of the permission group to delete.
    * `ajaxMode` : specify whether to access in AJAX/JSON mode.  `"true"` or `"false"`. 
  + Response (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * `CANNOT_DELETE_NEWUSERS` : Cannot delete default group 'Users'.
    * `CANNOT_DELETE_ADMIN` : Cannot delete default group 'Admin'.
    * `NO_DATA` : No request data was received by the script.
  + Possible successes generated (stored in `$_SESSION['userAlerts']`): 
    * `PERMISSION_DELETION_SUCCESSFUL_NAME` : Permission group successfully deleted.


- **delete_user.php**
  + Delete a user account, and all their permission associations.  TODO: prevent root account (`id`=1) from being deleted.
  + Default permission level: *admin only*
  + Request method: *POST*
  + Request parameters:
    * `user_id` : the id of the user to delete.
    * `ajaxMode` : specify whether to access in AJAX/JSON mode.  `"true"` or `"false"`. 
  + Response (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * `SQL_ERROR` : Database error.
    * `NO_DATA` : No request data was received by the script.
  + Possible successes generated (stored in `$_SESSION['userAlerts']`): 
    * `ACCOUNT_DELETIONS_SUCCESSFUL` : User account successfully deleted.


- **load_current_user.php**
  + Loads data for the currently logged in user.
  + Default permission level: *public*
  + Request method: *GET*
  + Request parameters: *none*
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Set to 0.
  + Response on success (JSON):
    * A JSON object containing relevant data for the user's account:
        * `id` : the id of the currently logged in user.
        * `username` : the username of the currently logged in user.
        * `displayname` : the display name of the currently logged in user.
        * `title` : the title of the currently logged in user.
        * `email` : the email address of the currently logged in user.
        * `sign_up_stamp` : the UNIX timestamp (seconds since Jan 1, 1970) of the time when the currently logged in user's account was created.
        * `admin` : specify whether or not this user is an admin.  Useful for frontend rendering purposes.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied.
    * User not logged in.
    * Account data not found.
    * `SQL_ERROR` : Database error.

    
- **load_permissions.php**
  + Returns a list of data for all permission levels.
  + Default permission level: *admin only*
  + Request method: *GET*
  + Request parameters:
    * `limit` : (optional).  Limit how many results to return.
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Set to 0.
  + Response on success (JSON):
    * A JSON object with permission id's (from `uc_permissions`) as keys, mapping to permission objects for each permission group.  Each permission object contains the following fields:
        - `id` : the unique id for this permission group.
        - `name` : the name of the permission group.
        - `is_default` : specify whether the permission group is automatically added when a new account is registered. (`"1"` or `"0"`).
        - `can_delete` : specify whether the permission group can be deleted via "delete_permission.php". (`"1"` or `"0"`).
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied.
    * Database error.


- **load_site_pages.php**
  + Scans the root directory for new and missing PHP pages, and updates the database accordingly (new pages are made public by default).  Then, returns a list of data objects corresponding to website pages.
  + Default permission level: *admin only*
  + Request method: *GET*
  + Request parameters: *none*
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Set to 0.
  + Response on success (JSON):
    * A JSON object with file names as keys, mapping to page objects for each page.  Each page object contains the following fields:
        - `id` : the unique id for this page.
        - `page` : the file name of the page, relative to the root directory.
        - `private` : specify whether the page is public or private (logged in users only). (`"1"` or `"0"`).
        - `status` : returns the status of the page after the most recent call to this function, indicating whether it was created, deleted, or updated. (`"C"`, `"D"`, or `"U"`).
        - `permissions` : A list of permissions associated with this page, as per load_permissions.php.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied.
    * Database error.
    

- **load_site_settings.php**
  + Returns a list of site configuration parameters, indexed by the parameter name.
  + Default permission level: *admin only*
  + Request method: *GET*
  + Request parameters: *none*
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Set to 0.
  + Response on success (JSON):
    * A JSON object with parameter names as keys, mapping to their values.  The default parameters for UserFrosting are as follows:
        - `website_name` : the name of your site.
        - `website_url` : the root url for your UserFrosting installation.
        - `email` : the email address to use for sending administrative emails.
        - `activation` : `"true"` if email activation for new accounts is enabled, `"false"` otherwise.
        - `resend_activation_threshold` : number of hours required between consecutive requests from a user to resend their account activation link.  Default is 0.
        - `language` : The language template to use for generating site messages, etc.  Default: english.
        - `template` : The default site CSS template.  TODO: expand to include Bootstrap templates.
        - `new_user_title` : The default title for newly registered users.
        - `can_register` : `"true"` if account self-registration is enabled (from "register.php"), `"false"` otherwise.
        - `language_options` : A list of language template options, based on scanning the `models/languages` folder.
        - `template_options` : A list of CSS template options, based on scanning the `models/site-templates` folder.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied.
    * Database error.

        
- **load_user.php**
  + Load data for a specified user, using the user's user id (`id` column in `uc_users`).
  + Default permission level: *admin only*
  + Request method: *GET*
  + Request parameters:
    * `user_id` : a user's user id.
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Obviously set to 0.
  + Response on success (JSON):
    * A JSON object containing the user's data.  Contains the following fields:
        - `user_id` : the unique id of the specified user.
        - `user_name` : the username of the specified user.
        - `display_name` : the display name of the specified user.
        - `title` : the title of the specified user.
        - `email` : the email address of the specified user.
        - `sign_up_stamp` : the UNIX timestamp (seconds since Jan 1, 1970) of the time when the specified user's account was created.
        - `last_sign_in_stamp` : the UNIX timestamp (seconds since Jan 1, 1970) of the time when the user last logged in.
        - `active` : `"1"` if the user's account has been activated, `"0"` otherwise.
        - `enabled` : `"1"` if the user's account is enabled, `"0"` otherwise.
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * Invalid user id specified
    * Database error
    

- **load_users.php**
  + Load data objects for all users.
  + Default permission level: *admin only*
  + Request method: *GET*
  + Request parameters:
    * `limit` : (optional).  Limit how many results to return.
  + Response on failure (JSON):
    * `errors` : the number of errors generated by the request.
    * `successes` : the number of successes generated by the request.  Obviously set to 0.
  + Response on success (JSON):
    * A JSON object with user id's (from `uc_users`) as keys, mapping to user objects.  Each user object contains the same fields as specified in "load_user.php".
  + Possible errors generated (stored in `$_SESSION['userAlerts']`):
    * Page access denied
    * Database error
    
    
TODO Tasks
----------
These are features to be added in future releases.

1. Add support for `https`.  Live users will need to purchase an SSL certificate, but we also need to see what changes must be made to the code to support https.
2. Add **OAuth** support, for users to create accounts and log in via Facebook/Google.
3. Implement **root** account.  Root account is created upon install, can never be deleted, and has permissions for every page, regardless of their permission group membership.
4. Add ability for admins to modify permission group names/settings.
5. Filter user list by name, group membership, etc.
6. Full directory/subdirectory management tool for pages.
7. Add ability for admins to add/remove user account fields, without having to modify code.
8. Deploy the `jqueryvalidator` plugin for client-side validation (as opposed to our own, clunkier validator).
9. Admin-side user account creation should bypass activation process.
10. Associate permission groups with allowed actions, rather than individual pages.  Actions, in turn, are linked to pages (on the backend) and features (on the frontend).  Automatically hide features for which a user does not have permission.
11. Convert to standard REST architecture, implementing updates as PUT and deletes as DELETE.  This could mean that different backend pages that act on the same type of object (users, pages, etc) could be combined into a single page that takes different actions depending on the request method.

Creds
-----

The back end account management system is derived from [UserCake 2.0.2](http://usercake.com), while the dashboard and admin account features are based on the SB Admin Template by [Start Bootstrap](http://startbootstrap.com). Other key frameworks and plugins used in this system are:

*  jQuery 1.10.2
*  Twitter Bootstrap 3.0
*  [Font Awesome](http://fontawesome.io)
*  [Handlebars templating](http://handlebarsjs.com/)
*  [Tablesorter 2.0](http://tablesorter.com)
*  [DateJS](http://www.datejs.com)
*  [Bootstrap Switch](http://bootstrap-switch.org) 

All components are copyright of their respective creators.