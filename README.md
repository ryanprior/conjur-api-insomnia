# Conjur.org Insomnia Workspace

With Insomnia and this Conjur.org workspace, you have a convenient and powerful
graphical interface for learning, testing, and demonstrating the Conjur API.

On a scale of `curl` to the full Conjur Enterprise UI, the Conjur.org Insomnia
Workspace is here:

```
                                        more graphical, semantic, high level ->
[curl]--------------[*]-----------------------------------------[Enterprise UI]
<- more textual, literal, low level
```

## Prerequisites

1. An account on a Conjur server.
   
   The easiest way to get this is to [sign up for a hosted evaluation
   account][get-started].
   
   *License: GNU LGPL*
   
   You could also [install Conjur yourself][install-conjur].
2. Insomnia
   
   Download: https://insomnia.rest/
   
   *License: MIT*

[get-started]: https://www.conjur.org/get-started/try-conjur.html
[install-conjur]: https://www.conjur.org/get-started/install-conjur.html

## Importing the workspace

To import the Conjur.org workspace:
1. Clone this repository.
2. Open Insomnia. In the top left, you'll notice a purple "Insomnia" header -
   this is your workspace switcher. Click it and choose the "Import/Export"
   option.
3. Choose Import Data, then From File. Select the "Conjur.org.json" in this
   folder. You should see an "Import Succeeded" dialog appear; click OK.

## Initializing the workspace

Before you can use the Conjur API, the workspace needs to be initialized with
some values specific to your environment.

### Connecting to the Conjur server

In order for Insomnia to connect to the Conjur server, it needs the server's
endpoint URL and your account.

1. click the text that says "No Environment" in the top
left, then select "Manage Environments" at the bottom of that menu. This opens
the environment editor.
2. Enter in your account and the URL of the Conjur server you're using, if
different from the eval server that's pre-filled. You get the account when you
fill out the form to create an eval account, or if you're hosting Conjur
yourself, it's the account you created with `conjurctl account create`.
3. Press "Done" at the lower right corner of the editor. If the done button is
disabled, check your syntax for balanced quotes and mistaken keystrokes. Each
environment needs to be a valid JSON document.

#### Check server health

At this point, you should be able to check the health of the server:
1. In the left sidebar, choose "Verify the server is healthy"
2. In the top bar towards the middle right, click "Send."
3. Verify in the right-hand pane that the response comes in saying "200 OK"

##### Troubleshooting

If it says something else, go back to the environment editor and check your
server URL. It should start with `https://` (or `http://` if you're testing a
local insecure installation) and shouldn't have any trailing `/api`.

### Set up the admin user

Now that we're connected to the server, we can set up the first user:
1. Switch to the "admin" environment by clicking "No Environment" in the top
   left and selecting "Use admin."
2. Click "admin" and select "Manage Environments" to bring up the environment
   editor again. This time it shows the admin sub-environment.
3. Enter the admin's API key. You get this API key when you fill out the form to
   create an eval account, or when you create an account on your own Conjur
   server.
   
   *Note: the API key goes into the `"api_key` field, not the `"password"` field."*
4. Press "Done"

#### Authenticate as admin

In the left sidebar, select "Authenticate" and click "Send." You should get back
a "200 OK" result.

##### Troubleshooting

If you get a `404 Not Found` result, check the account parameter in your base
environment editor.

If you get a `401 Not Authorized` result:
1. Make sure you are using the admin environment. It should say Conjur.org in
   the top left, then below that, a red circle with "admin"
2. Check the API key in the admin environment editor. There should be no extra
   spaces or stray characters.

#### Set an admin password (optional)

If you want to set a password for the admin user, select "Change your password"
on the left-hand side and click "Send." You will be prompted to type a new
password. You should get a "204 No Content" response.

Now open the environment editor and add the password to the environment's
`password` field.

If you set a password, test that it works by selecting "Login" on the left-hand
side and clicking "Send." You should get back a "200 OK" response along with the
admin's API key.

##### Troubleshooting

If you get a `401 Not Authorized` result, double check that you added the
password to the admin sub-environment and that it's the same one you typed when
you were prompted.

### Load a policy

Next, we'll load a policy that will create users, groups, and other objects for
us to manipulate using the API.

1. Right-click on the "Policy" folder in the left-hand pane and select
   "Environment." This opens the environment editor specifically for APIs in
   this folder.
2. In the environment is a template tag that says "File" and initially it will
   be red. Click it, then in the dialog that appears, click "policy.yml"
   underneath "Choose File."
3. In the file selector that appears, locate and select the `policy.yml` file in
   this folder. Click "Import" and then "Done." The file template tag should be
   blue now.
4. Click "Done" in the environment editor.
5. Select "Replace a policy" in the left-hand pane and click "Send." You should
   get a "201 Created" response.

##### Troubleshooting

If you get a `401 Not Authorized` result:
1. Make sure you are using the admin environment. It should say Conjur.org in
   the top left, then below that, a red circle with "admin"
2. If it's been a few minutes since you last authenticated, select
   "Authenticate" on the left-hand side and click "Send** again.

**Important Conjur API Tip:** the auth token you get from the "Authenticate" API
endpoint has a time-to-live of eight minutes. Every time it expires, you'll need
to authenticate again. The Conjur CLI and Conjur API clients typically do this
for you, but in Insomnia you'll have to remember to do this. Any time you see
`401 Unauthorized` it could be because your token is expired.

### Set up the non-admin users

Your policy creates three non-admin users with various different roles. For a
description of these users and their roles, see the [Intro to Conjur
policy][policy-intro] from which the Insomnia workspace policy is derived.

[policy-intro]: https://www.conjur.org/get-started/key-concepts/intro-to-conjur-policy.html

When you first load the policy, the JSON you get back will contain the API keys
for each created user.

If you loaded the policy previously, you can't get the API keys again by loading
the policy. Instead, you'll want to use "Rotate another API key" to rotate them.

For each user:
1. Switch to that user's sub-environment
2. Open the environment editor and enter that user's API key
3. Test that you can "Authenticate"
4. Optionally, set a password

### That's It

Now you're set up with a fully functional Conjur.org Insomnia workspace.
