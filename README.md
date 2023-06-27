# Budibase-embeddedscript

This append scripts to your current screen and use a table to store them.

# Description

Embed js scripts from your databse into your app

Find out more about [Budibase](https://github.com/Budibase/budibase).

## Instructions

Create a table with the following columns:

- content (text) JS code
- collection (options or text) Collection name to group scripts
- parent (options with values: parent, body, component) Where the script should be appended
- inBuilder (boolean) Does the script execute in Builder

  ![image](https://github.com/diogenesbrussels/budibase-embeddedscript/assets/91942877/aff598d6-6c8c-4788-b95b-4d950712e569)


Add the component, select your newly created table and filter by collection name if needed.

You can expose values to the handlebars context via the Context Object.

Example value
```
return { CurrentUser: $("Current User.email"), search: $("State.search_text")};
```
Then use {{ CurrentUser }} in your script's content

## Known issues

- Handlebars are parsed but can't access the live Budibase context

## Instructions for devs

To build your new plugin run the following in your Budibase CLI:

```
budi plugins --build
```

You can also re-build everytime you make a change to your plugin with the command:

```
budi plugins --watch
```
