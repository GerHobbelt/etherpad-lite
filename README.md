
# Introduction

This repo contains a modified version of the original etherpad-lite modified to natively run on cloudfoundry, possibly on an offline mode (ie. without requiring internet access)

# Installation


 1. Download the `etherpad-lite-cf.zip` in release tab on github.
 2. Extract the zip file
 3. Go to the extracted archive with your console
 4. 
    - Run `cf push etherpad-lite -m 512M` and you will have a sqlite instance on your app created
    - (With a database user provided) Follow this [instructions](#using-database-from-user-provided-service) and run `cf push etherpad-lite -m 512M`
 5. You're done

## Install new plugin(s)
To install new etherpad plugin (you can see a list here: [https://github.com/.../Plugin,-a-list](https://github.com/ether/etherpad-lite/wiki/Plugin,-a-list) ) you shoud add them in `package.json` in the root app directory to let the nodejs buildpack build/download them.


## Default plugins
It use a tested set of plugin by default in the package which you can remove by updating the `package.json`.
See the list:
 - [ep_ldapauth](https://github.com/ArthurHlt/ep_ldapauth) (Not using one from npm, it is not maintained): Hooks into etherpad lite auth to provide LDAP authentication.
 - [ep_tables](https://github.com/gedion/ep_tables): Adds tables to etherpad-lite
 - [ep_syntaxhighlighting](https://github.com/etinquis/etherpad-plugins): Adds syntax highlighting to etherpad-lite
 - [ep_list_pads](https://github.com/JohnMcLear/ep_list_pads.git): List Pads on the Index Page
 - [ep_scrollto](https://github.com/johnmclear/ep_scrollto): Scroll to a specific line number based on a parameter of lineNumber in the URL IE [http://test.com/p/foo#lineNumber=10](http://test.com/p/foo#lineNumber=10) -- Users can click on the line number to get a link
 - ep_colors: add colors to the etherpad
 - [ep_headings](https://github.com/fourplusone/etherpad-plugins): Adds heading support to Etherpad Lite
 - [ep_previewimages](https://github.com/JohnMcLear/ep_previewimages.git): Image previewer, paste the URL or an image or upload an image using ep_fileupload
 - [ep_tasklist](https://github.com/johnmclear/ep_tasklist): Task list in Etherpad
 - [ep_wrap](https://github.com/johnmclear/ep_wrap): Option to disable line wrapping
 - [ep_email_notifications](https://github.com/JohnMcLear/ep_email_notifications.git): Subscribe to a pad and receive an email when someone edits your pad
 - ep_autoscrolldown: Add a checkbox to auto-scroll down the pad when content is modified in Etherpad-Lite. This module is part of an academic project for courses accessibility from Paris 8 University.
 - ep_historicalsearch: Search through the history of documents to find when a query/search pattern or string existed
 - ep_markdownify: Inline markdown formating. Format headings, show images, highlight lists, tables and more
 - [ep_table_of_contents](https://github.com/JohnMcLear/ep_table_of_contents.git): View a table of contents for your pad
 - [ep_prompt_for_name](https://github.com/JohnMcLear/ep_prompt_for_name.git): Prompt an author for their name
 - [ep_adminpads](https://github.com/spcsser/ep_adminpads) Plugin for etherpad lite for admins to list, search and delete pads.
 - [ep_pad_tracker](https://github.com/aoberegg/ep_pad_tracker) With the Pad Tracker it's possible to search in all the created Pads of your server and with a click on track you can track it. Tracking means that you can see all tracked-pads on the main-page (/admin/padtracker) of the Plugin. All tracked Pads get listed on the main page with the name, the amoung of revisions and the date of the last edit.

## Using database

 1. Create a database service which have an uri, the name must follow this regex: `/.*(db|database|pg|postgres|mysql|mongo|lite|level|dirty|redis|couch|elasticsearch).*/i`
 2. Bind your service to your app `cf bind-service <app> <service name>`
 3. you need to... No that's all, push your app :)
 
## Using LDAP from user provided service
By default this etherpad-lite has the [ep_ldapauth](https://github.com/ArthurHlt/ep_ldapauth) running and you can directly use it with user provided service, do this steps:


 1. First you need to create your user provided service, in a terminal run `cf cups myldap -p 'same json from ldap plugin` (**Note**: your service name must have `ldap` in his name for auto-binding)
 2. Bind your service to your app `cf bind-service <app> myldap`
 3. Push your app

Cf cups example with json:

```shell
$ cf cups myldap -p '{"url": "ldaps://ldap.example.com", "accountBase": "ou=Users,dc=example,dc=com", "accountPattern": "(&(objectClass=*)(uid={{username}}))", "displayNameAttribute": "cn", "searchDN": "uid=searchuser,dc=example,dc=com", "searchPWD": "supersecretpassword", "groupSearchBase": "ou=Groups,dc=example,dc=com", "groupAttribute": "member", "groupAttributeIsDN": true ,"searchScope": "sub", "groupSearch": "(&(cn=admin)(objectClass=groupOfNames))", "anonymousReadonly": false}'
```

## Current limitation

* etherpad-lite does not support being instanciated as a cluster. Prefer a single instance `-i 1` in your Cf push. This implies that unplanned maintenances of instances crash won't be transparent to etherpad-lite users

## Note
This version can be runned offline (with no access to internet) if you don't add any plugin in `package.json`

# License
[Apache License v2](http://www.apache.org/licenses/LICENSE-2.0.html)
